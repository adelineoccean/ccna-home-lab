# Phase 1 — Basic 2-PC LAN

## Objective
Build a basic two-PC LAN in Cisco Packet Tracer to verify the lab
environment is working and demonstrate fundamental switching concepts.

## Topology

PC1---|SW1|---PC2

## IP Addressing

| Device | Interface | IP Address | Subnet Mask |
|---|---|---|---|
| PC1 | FastEthernet0 | 192.168.1.1 | 255.255.255.0 |
| PC2 | FastEthernet0 | 192.168.1.2 | 255.255.255.0 |


## Configurations Applied

- SW1 hostname set to SW1
- SW1 enable secret configured
- SW1 console and VTY passwords configured
- SW1 banner MOTD configured
- Service password-encryption enabled

## Verification

- PC1 successfully pinged PC2 ✅
- PC2 successfully pinged PC1 ✅

## Files

| File | Description |
|---|---|
| `packet-tracer-files/phase1-Basic 2pc-lan.pkt` | Packet Tracer topology file |
| `configs/SW1-config.txt` | SW1 running configuration |
| `screenshots/phase1-basic-lan/` | Verification screenshots |

## Key Concepts Demonstrated

- Basic switch configuration
- IPv4 addressing
- End-to-end Layer 2 connectivity verification
- IOS CLI fundamentals