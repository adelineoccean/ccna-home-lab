# Phase 3 — Switching, VLANs & Inter-VLAN Routing

## Objective
Build a multi-switch topology with VLANs, 802.1Q trunk links, and
inter-VLAN routing using the Router-on-a-Stick method. Configure port
security on access ports to demonstrate security awareness.

## Topology
PC1 (VLAN10) --|                    |-- PC3 (VLAN20)
PC2 (VLAN10) --| SW1 --- Trunk --- SW2 |-- PC4 (VLAN20)
PC5 (VLAN30) --|                    |-- PC6 (VLAN30)
|
Trunk
|
R1
(Router-on-a-Stick)

## VLAN Design

| VLAN ID | Name | Purpose |
|---|---|---|
| 10 | Engineering | Engineering department PCs |
| 20 | Sales | Sales department PCs |
| 30 | HR | HR department PCs |
| 99 | Management | Switch management traffic |
| 1 | Native | Default native VLAN (unused for data) |

## IP Addressing

| Device | Interface | IP Address | Subnet Mask | VLAN |
|---|---|---|---|---|
| R1 | Gig0/0.10 | 192.168.10.1 | 255.255.255.0 | 10 |
| R1 | Gig0/0.20 | 192.168.20.1 | 255.255.255.0 | 20 |
| R1 | Gig0/0.30 | 192.168.30.1 | 255.255.255.0 | 30 |
| R1 | Fa0/0.99 | 192.168.99.1 | 255.255.255.0 | 99 |
| SW1 | VLAN99 | 192.168.99.10 | 255.255.255.0 | 99 |
| SW2 | VLAN99 | 192.168.99.20 | 255.255.255.0 | 99 |
| PC1 | Fa0 | 192.168.10.10 | 255.255.255.0 | 10 |
| PC2 | Fa0 | 192.168.10.11 | 255.255.255.0 | 10 |
| PC3 | Fa0 | 192.168.20.10 | 255.255.255.0 | 20 |
| PC4 | Fa0 | 192.168.20.11 | 255.255.255.0 | 20 |
| PC5 | Fa0 | 192.168.30.10 | 255.255.255.0 | 30 |
| PC6 | Fa0 | 192.168.30.11 | 255.255.255.0 | 30 |

## Key Concepts Demonstrated

### VLANs
VLANs logically segment a physical network into separate broadcast
domains. Traffic in VLAN 10 is completely isolated from VLAN 20 and
VLAN 30 at Layer 2 — even though all devices share the same physical
switches. This improves security and reduces unnecessary broadcast
traffic across the network.

### 802.1Q Trunking
Trunk links between SW1, SW2, and R1 carry traffic for all VLANs
simultaneously. Each frame is tagged with its VLAN ID using 802.1Q
encapsulation so the receiving device knows which VLAN the frame
belongs to. VLAN 99 is configured as the native VLAN on all trunk
ports — native VLAN traffic is sent untagged across the trunk.

### Router-on-a-Stick (Inter-VLAN Routing)
A single physical interface on R1 (Gig0/0) is divided into logical
sub-interfaces — one per VLAN. Each sub-interface is configured with
802.1Q encapsulation and an IP address that serves as the default
gateway for that VLAN. This allows hosts in different VLANs to
communicate by routing traffic through R1 without needing a separate
physical router interface per VLAN.


## Configuration Summary

### SW1
- VLANs 10, 20, 30, 99 created and named
- Fa0/3 and Fa0/4 configured as VLAN 10 access ports
- Fa0/5 configured as VLAN 30 access port
- Fa0/1 configured as trunk to R1 (native VLAN 99)
- Fa0/2 configured as trunk to SW2 (native VLAN 99)
- VLAN 99 management IP: 192.168.99.10
- Port security enabled on Fa0/3 and Fa0/4 (sticky, maximum 1, restrict)
- Enable secret, console/VTY passwords, service password-encryption
- Banner MOTD configured

### SW2
- VLANs 10, 20, 30, 99 created and named
- Fa0/3 and Fa0/4 configured as VLAN 20 access ports
- Fa0/5 configured as VLAN 30 access port
- Fa0/2 configured as trunk to SW1 (native VLAN 99)
- VLAN 99 management IP: 192.168.99.20
- Enable secret, service password-encryption
- Banner MOTD configured

### R1
- Gig0/0 physical interface enabled with no IP (sub-interfaces carry traffic)
- Gig0/0.10 — encapsulation dot1q 10, gateway for VLAN 10
- Gig0/0.20 — encapsulation dot1q 20, gateway for VLAN 20
- Gig0/0.30 — encapsulation dot1q 30, gateway for VLAN 30
- Gig0/0.99 — encapsulation dot1q 99 native, gateway for VLAN 99

## Verification

### VLAN verification
SW1# show vlan brief
SW1# show interfaces trunk

All VLANs confirmed active. Trunk ports confirmed carrying
VLANs 10, 20, 30, and 99.

### Routing verification
R1# show ip interface brief
R1# show ip route

All sub-interfaces confirmed up/up. Routing table showed directly
connected routes for all four subnets.

### Ping tests

**Intra-VLAN (same subnet):**
| Source | Destination | Result |
|---|---|---|
| PC1 | PC2 (VLAN 10) | ✅ Success |
| PC3 | PC4 (VLAN 20) | ✅ Success |
| PC5 | PC6 (VLAN 30) | ✅ Success |

**Inter-VLAN (across router):**
| Source | Destination | Result |
|---|---|---|
| PC1 (VLAN 10) | PC3 (VLAN 20) | ✅ Success |
| PC1 (VLAN 10) | PC5 (VLAN 30) | ✅ Success |
| PC3 (VLAN 20) | PC5 (VLAN 30) | ✅ Success |

## Files

| File | Description |
|---|---|
| `packet-tracer-files/phase3-vlans-intervlan-routing.pkt` | Packet Tracer topology |
| `configs/phase3-ROAS-SW1-config.txt` | SW1 running configuration |
| `configs/phase3-ROAS-SW2-config.txt` | SW2 running configuration |
| `configs/phase3-ROAS-R1-config.txt` | R1 running configuration |
| `screenshots/phase3-vlans-intervlan-routing/` | Verification screenshots |

## Key Takeaways

- VLANs create logical network segmentation without additional hardware
- Trunk links carry multiple VLANs over a single physical connection
  using 802.1Q tagging
- Router-on-a-Stick enables inter-VLAN routing with a single router
  interface and sub-interfaces — practical for smaller deployments
- Native VLAN should always be changed from the default VLAN 1 as a
  security best practice
- Port security prevents unauthorized devices from connecting to
  access ports and is an important Layer 2 security control