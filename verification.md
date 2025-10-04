# Project 3: OSPF Multi-Router Office Network Verification

## 1. VLAN Verification
- Verified VLANs on SW1, SW2, SW3. All access ports assigned correctly.
- Screenshots: `screenshots/sw1_vlan.png`, `screenshots/sw2_vlan.png`, `screenshots/sw3_vlan.png`

## 2. MAC Address Table Verification
- Verified MAC addresses learned correctly on each switch.
- Screenshots: `screenshots/sw1_mac.png`, `screenshots/sw2_mac.png`, `screenshots/sw3_mac.png`

## 3. Router Interfaces Verification
- All LAN and Serial interfaces are up with correct IPs.
- Screenshots: `screenshots/r1_ip_int.png`, `screenshots/r2_ip_int.png`, `screenshots/r3_ip_int.png`

## 4. OSPF Neighbor Verification
- R1 sees R2, R2 sees R1 & R3, R3 sees R2.
- Screenshots: `screenshots/r1_ospf_neighbor.png`, `screenshots/r2_ospf_neighbor.png`, `screenshots/r3_ospf_neighbor.png`

## 5. OSPF Routing Table Verification
- All routers have OSPF learned routes to all LANs.
- Screenshots: `screenshots/r1_ospf_route.png`, `screenshots/r2_ospf_route.png`, `screenshots/r3_ospf_route.png`

## 6. Ping Tests
| Source | Destination | Result | Screenshot |
|--------|------------|--------|------------|
| PC1    | 192.168.10.1 | Success | `screenshots/pc1_ping_r1.png` |
| PC1    | 192.168.20.21 | Success | `screenshots/pc1_ping_pc3.png` |
| PC1    | 192.168.30.31 | Success | `screenshots/pc1_ping_pc5.png` |
| PC1    | 192.168.10.100 | Success | `screenshots/pc1_ping_server.png` |
| PC3    | 192.168.20.1 | Success | `screenshots/pc3_ping_r2.png` |
| PC3    | 192.168.10.11 | Success | `screenshots/pc3_ping_pc1.png` |
| PC3    | 192.168.30.31 | Success | `screenshots/pc3_ping_pc5.png` |
| PC5    | 192.168.30.1 | Success | `screenshots/pc5_ping_r3.png` |
| PC5    | 192.168.10.100 | Success | `screenshots/pc5_ping_server.png` |
| PC5    | 192.168.20.21 | Success | `screenshots/pc5_ping_pc3.png` |

## 7. Optional: Traceroute Verification
- PC1 to Server1 to verify path through routers.
- Screenshot: `screenshots/pc1_traceroute_server.png`

