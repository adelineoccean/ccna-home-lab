# Phase 3 — VLAN Basics Lab

## Objective
Learn how to configure VLANs on a Cisco switch and understand why a
Layer 3 device is required to communicate between them. Demonstrate
how VLANs segment a network into separate broadcast domains.

## Lab Purpose
VLANs help segment a network for easier administration and added
security. Understanding how VLANs work is an essential skill for
daily network engineering tasks — from troubleshooting connectivity
issues to designing secure network segments.

## Tool
Cisco Packet Tracer

## Topology
PC1 (VLAN10) -- Fa0/1 --|        |
PC2 (VLAN10) -- Fa0/2 --| SW1 (2960  )  |
PC3 (VLAN20) -- Fa0/3 --|  |
PC4 (VLAN20) -- Fa0/4 --|        |

## VLAN Design

| VLAN ID | Name | Subnet | Ports |
|---|---|---|---|
| 10 | Sales | 192.168.10.0/24 | Fa0/1, Fa0/2 |
| 20 | Admin | 192.168.20.0/24 | Fa0/3, Fa0/4 |

## IP Addressing

| Device | IP Address | Subnet Mask | VLAN |
|---|---|---|---|
| PC1 | 192.168.10.1 | 255.255.255.0 | 10 |
| PC2 | 192.168.10.2 | 255.255.255.0 | 10 |
| PC3 | 192.168.20.1 | 255.255.255.0 | 20 |
| PC4 | 192.168.20.2 | 255.255.255.0 | 20 |

## Lab Tasks

### Task 1 — Physical connections
Connected four hosts to SW1 (Cisco 2960) using straight-through
cables. Noted which hosts connect to which interfaces before
configuring VLANs to ensure correct port assignments.

### Task 2 — IP addressing
Assigned IP addresses to each host from within their respective
VLAN subnets via Desktop → IP Configuration in Packet Tracer.
VLAN 10 hosts use the 192.168.10.0/24 range and VLAN 20 hosts
use the 192.168.20.0/24 range.

### Task 3 — VLAN and port configuration
Created VLANs 10 and 20 on SW1 and assigned ports to each VLAN.
Ports were forced into access mode using `switchport mode access`
to prevent them from negotiating trunk links:

SW1(config)# vlan 10
SW1(config-vlan)# name Sales
SW1(config-vlan)# interface fa0/1
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config-if)# interface fa0/2
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 10
SW1(config)# vlan 20
SW1(config-vlan)# name Admin
SW1(config-vlan)# interface fa0/3
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 20
SW1(config-if)# interface fa0/4
SW1(config-if)# switchport mode access
SW1(config-if)# switchport access vlan 20

### Task 4 — Verify VLAN assignments
Used `show vlan brief` to confirm all ports were placed in their
correct VLANs. Default VLAN 1 (native) retained all unassigned
ports as expected.

### Task 5 — Connectivity testing
Tested pings between hosts to confirm VLAN segmentation behavior:

**Intra-VLAN pings (expected: success)**
| Source | Destination | Result |
|---|---|---|
| PC1 (192.168.10.1) | PC2 (192.168.10.2) | ✅ Success |
| PC3 (192.168.20.1) | PC4 (192.168.20.2) | ✅ Success |

**Inter-VLAN pings (expected: fail — no router)**
| Source | Destination | Result |
|---|---|---|
| PC1 (192.168.10.1) | PC3 (192.168.20.1) | ❌ Failed (expected) |
| PC2 (192.168.10.2) | PC4 (192.168.20.2) | ❌ Failed (expected) |

The failed inter-VLAN pings confirm that VLANs are correctly
isolating traffic at Layer 2. A Layer 3 device (router or Layer 3
switch) is required to route traffic between VLANs — demonstrated
in the Router-on-a-Stick lab in phase3-vlans-intervlan-routing.md.

## Key Concepts Demonstrated

- VLANs create separate Layer 2 broadcast domains on a single switch
- Hosts in the same VLAN can communicate directly through the switch
- Hosts in different VLANs cannot communicate without a Layer 3 device
  regardless of whether they are connected to the same physical switch
- `switchport mode access` prevents a port from negotiating trunking
  via DTP (Dynamic Trunking Protocol) — a security best practice
- VLAN names (Sales, Admin) make administration easier and more
  intuitive than referring to VLAN IDs alone
- By default all ports belong to VLAN 1 (native/default) until
  explicitly assigned to another VLAN

## Key Commands Reference

| Command | Purpose |
|---|---|
| `vlan 10` | Create VLAN 10 |
| `name Sales` | Assign a name to the VLAN |
| `switchport mode access` | Force port into access mode |
| `switchport access vlan 10` | Assign port to VLAN 10 |
| `show vlan brief` | Verify VLAN assignments and port membership |

## Files

| File | Description |
|---|---|
| `packet-tracer-files/phase3-vlan-basics-lab.pkt` | Packet Tracer topology |
| `configs/SW1-vlan-basics-config.txt` | SW1 running configuration |
| `screenshots/phase3-vlans/` | Verification screenshots |

## Notes
- VLAN naming is optional but strongly recommended in real networks
  for clarity and documentation purposes
- Inter-VLAN routing using Router-on-a-Stick is covered in
  phase3-vlans-intervlan-routing.md
- A Layer 3 switch can also perform inter-VLAN routing using Switched
  Virtual Interfaces (SVIs) — an alternative to Router-on-a-Stick
  covered in a later phase