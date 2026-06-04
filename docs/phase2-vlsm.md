# Phase 2 — VLSM Subnetting & Network Fundamentals

## Objective
Design and implement a Variable Length Subnet Mask (VLSM) addressing
scheme across three subnets of different sizes, configure a router with
multiple interfaces, and verify inter-subnet routing and connectivity.

## Topology                  
PC1 --| PC2 --| SW1 -- R1 -- SW2  |-- PC3 |-- PC4
                       |
                      SW3
                     /	 \
                    PC5 PC6

## VLSM Addressing Design

Starting network: 192.168.1.0/24

| Subnet | Department | Hosts Needed | Network Address | CIDR | Subnet Mask | Host Range | Broadcast |
|---|---|---|---|---|---|---|---|
| 1 | Engineering | 50 | 192.168.1.0 | /26 | 255.255.255.192 | .1 — .62 | .63 |
| 2 | Sales | 25 | 192.168.1.64 | /27 | 255.255.255.224 | .65 — .94 | .95 |
| 3 | Management | 10 | 192.168.1.96 | /28 | 255.255.255.240 | .97 — .110 | .111 |

### Why VLSM?
Traditional fixed-length subnetting would assign the same size subnet
to every segment regardless of need, wasting addresses. VLSM allows
each subnet to be sized precisely for its host count, making more
efficient use of the address space.

## Device IP Addressing

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| R1 | Gig0/0 | 192.168.1.1 | 255.255.255.192 | — |
| R1 | Gig0/1 | 192.168.1.65 | 255.255.255.224 | — |
| R1 | Gig0/2 | 192.168.1.97 | 255.255.255.240 | — |
| PC1 | Fa0 | 192.168.1.10 | 255.255.255.192 | 192.168.1.1 |
| PC2 | Fa0 | 192.168.1.11 | 255.255.255.192 | 192.168.1.1 |
| PC3 | Fa0 | 192.168.1.70 | 255.255.255.224 | 192.168.1.65 |
| PC4 | Fa0 | 192.168.1.71 | 255.255.255.224 | 192.168.1.65 |
| PC5 | Fa0 | 192.168.1.100 | 255.255.255.240 | 192.168.1.97 |
| PC6 | Fa0 | 192.168.1.101 | 255.255.255.240 | 192.168.1.97 |

## Router Configuration Summary

R1 was configured with three GigabitEthernet interfaces, one per subnet.
Each interface acts as the default gateway for its respective subnet.
All interfaces were brought up with the `no shutdown` command.

Key commands used:
- `interface gig0/0` — enter interface config mode
- `ip address` — assign IP and subnet mask
- `no shutdown` — bring interface up
- `description` — label each interface for documentation
- `write memory` — save configuration to NVRAM

## Verification

### Intra-subnet pings (same subnet)
| Source | Destination | Result |
|---|---|---|
| PC1 | PC2 | ✅ Success |
| PC3 | PC4 | ✅ Success |
| PC5 | PC6 | ✅ Success |

### Inter-subnet pings (across router)
| Source | Destination | Result |
|---|---|---|
| PC1 | PC3 (Sales) | ✅ Success |
| PC1 | PC5 (Management) | ✅ Success |
| PC3 | PC5 (Management) | ✅ Success |

### show ip interface brief
All three R1 interfaces confirmed status: up, line protocol: up.

### show ip route
R1 routing table showed directly connected routes for all three
subnets — C 192.168.1.0/26, C 192.168.1.64/27, C 192.168.1.96/28.

## Simulation Mode Observation
Used Packet Tracer simulation mode to trace a packet from PC1 to PC3.
Observed the packet travel PC1 → SW1 → R1 → SW2 → PC3, with Layer 2
MAC addresses rewritten at R1 at each hop while the Layer 3 IP
addresses remained unchanged end-to-end. This demonstrates how routing
operates at Layer 3 while switching operates at Layer 2.

## Key Concepts Demonstrated

- VLSM subnetting from a single /24 address space
- Router interface configuration and multi-interface routing
- Default gateway role in inter-subnet communication
- Layer 2 vs Layer 3 behavior observed via simulation mode
- IOS CLI verification commands (show ip route, show ip interface brief)

## Files

| File | Description |
|---|---|
| `packet-tracer-files/phase2-vlsm.pkt` | Packet Tracer topology |
| `configs/phase2-R1-config.txt` | R1 running configuration |
| `screenshots/phase2-vlsm/` | Verification screenshots |
