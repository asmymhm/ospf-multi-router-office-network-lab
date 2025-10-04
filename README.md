# Project 3 — OSPF Multi-Router Office Network
---
Table of Contents

1.  [Project Overview](#project-overview)
2.  [Project Objectives](#project-objectives)
3.  [Network Topology](#network-topology)
4.  [Device Interface Table](#device-interface-table)
5.  [IP Addressing Table](#ip-addressing-table)
6.  [Lab Steps](#lab-steps)
7.  [Device Configuration](#device-configuration)
	- [Router 1](#router-1)
	- [Router 2](#router-2)
	- [Router 3](#router-3)
	- [Switch 1](#switch-1)
	- [Switch 2](#switch-2)
	- [Switch 3](#switch-3)
	- [PC Configuration](#pc-configuration)
	- [Server Configuration](#server-configuration)
8.  [Verification](#verification)
9.  [Folder Structure](#folder-structure)

---
## Project overview

Implement OSPF across multiple routers to demonstrate dynamic routing in an enterprise environment. 
The lab uses Packet Tracer.

---
## Project Objective

- Configure OSPF area 0 across 3 routers.
- Configure serial WAN links (/30) between routers.
- Configure LANs for 3 sites with PCs and a server.
- Verify OSPF adjacency, routing, and end-to-end connectivity.

---
## Network Topology
- 3 Routers (R1, R2, R3)
- 3 Switches (SW1, SW2, SW3)
- 6 PCs (PC1..PC6) — 2 per LAN
- 1 Server (Server1) in LAN1 (on SW1)

<img src="topology\topology_overview.drawio.png" alt="TOPOLOGY OVERVIEW" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## Subnets
- LAN1: 192.168.10.0/24 (R1)
- LAN2: 192.168.20.0/24 (R2)
- LAN3: 192.168.30.0/24 (R3)
- WAN1 (R1-R2): 10.0.12.0/30
- WAN2 (R2-R3): 10.0.23.0/30

---
## Device Interface table

Connections:

R1 G0/0  <->  SW1 Fa0/1
R2 G0/0  <->  SW2 Fa0/1
R3 G0/0  <->  SW3 Fa0/1

R1 S0/3/0 <-> R2 S0/3/1  (Serial DCE <-> DTE)  network 10.0.12.0/30
R2 S0/3/0 <-> R3 S0/3/0  (Serial DCE <-> DTE)  network 10.0.23.0/30

PC1 Fa0/2 <-> SW1 Fa0/2
PC2 Fa0/3 <-> SW1 Fa0/3
Server1 Fa0/4 <-> SW1 Fa0/4

PC3 Fa0/2 <-> SW2 Fa0/2
PC4 Fa0/3 <-> SW2 Fa0/3

PC5 Fa0/2 <-> SW3 Fa0/2
PC6 Fa0/3 <-> SW3 Fa0/3

---
## IP addressing 

| Device | Interface / Notes | IP Address / Mask  | Gateway      |
|--------|-------------------|------------------- |------------- |
| PC1    | LAN1              | 192.168.10.11/24   | 192.168.10.1 |
| PC2    | LAN1              | 192.168.10.12/24   | 192.168.10.1 |
| PC3    | LAN2              | 192.168.20.21/24   | 192.168.20.1 |
| PC4    | LAN2              | 192.168.20.22/24   | 192.168.20.1 |
| PC5    | LAN3              | 192.168.30.31/24   | 192.168.30.1 |
| PC6    | LAN3              | 192.168.30.32/24   | 192.168.30.1 |
| Server1| LAN1              | 192.168.10.100/24  | 192.168.10.1 |

Router addresses:

| Device | Interface | IP/Mask            |
|--------|-----------|--------------------|
| R1     | G0/0      | 192.168.10.1/24    |
| R1     | S0/3/0    | 10.0.12.1/30 (DCE) |
| R2     | G0/0      | 192.168.20.1/24    |
| R2     | S0/3/1    | 10.0.12.2/30 (DCE) |
| R2     | S0/3/0    | 10.0.23.1/30 (DTE) |
| R3     | G0/0      | 192.168.30.1/24    |
| R3     | S0/3/0    | 10.0.23.2/30 (DTE) |

---
## Lab steps

1. Build topology in Packet Tracer (place devices & connect cables).
2. Configure PCs and Server (static IPs).
3. Configure switches .
4. Configure routers interfaces and set clock rate on DCE serials.
5. Configure OSPF (area 0) on all routers.
6. Verify OSPF neighbors and learned routes.
7. Test end-to-end pings (PC to PC, PC to Server).
8. Capture screenshots.

---
## Device Configuration

## Router 1 

! R1 config
```text
hostname R1
no ip domain-lookup
```
```text
interface GigabitEthernet0/0
description LAN to SW1
ip address 192.168.10.1 255.255.255.0
no shutdown
```
```text
interface serial0/3/0
description Serial to R2 (10.0.12.0/30) - DCE
ip address 10.0.12.1 255.255.255.252
clock rate 64000
no shutdown
```
```text
router ospf 1
network 192.168.10.0 0.0.0.255 area 0
network 10.0.12.0 0.0.0.3 area 0
```
```text
line con 0
logging synchronous
```
```text
end
write
```
---
## Router 2

! R2 config
```text
hostname R2
no ip domain-lookup
```
```text
interface GigabitEthernet0/0
description LAN to SW2
ip address 192.168.20.1 255.255.255.0
no shutdown
```
```text
interface Serial0/3/0
description Serial to R1 (10.0.12.0/30) - DTE
ip address 10.0.12.2 255.255.255.252
no shutdown
```
```text
interface Serial0/3/1
description Serial to R3 (10.0.23.0/30) - DCE
ip address 10.0.23.1 255.255.255.252
clock rate 64000
no shutdown
```
```text
router ospf 1
network 192.168.20.0 0.0.0.255 area 0
network 10.0.12.0 0.0.0.3 area 0
network 10.0.23.0 0.0.0.3 area 0
```
```text
line con 0
logging synchronous
```
```text
end
write
```
---
## Router 3

! R3 config
```text
hostname R3
no ip domain-lookup
```
```text
interface GigabitEthernet0/0
description LAN to SW3
ip address 192.168.30.1 255.255.255.0
no shutdown
```
```text
interface Serial0/3/0
description Serial to R2 (10.0.23.0/30) - DTE
ip address 10.0.23.2 255.255.255.252
no shutdown
```
```text
router ospf 1
network 192.168.30.0 0.0.0.255 area 0
network 10.0.23.0 0.0.0.3 area 0
```
```text
line con 0
logging synchronous
```
```text
end
write
```
---
## Switch 1

! SW1 config
```text
hostname SW1
no ip domain-lookup
```
!Create VLAN 10
```text
vlan 10
name HR
```
!Router uplink
```text
interface FastEthernet0/1
description Uplink to R1 G0/0
switchport mode access
switchport access vlan 10
no shutdown
```
!PCs and Server
```text
interface FastEthernet0/2
description PC1 - 192.168.10.11
switchport mode access
switchport access vlan 10
no shutdown

interface FastEthernet0/3
description PC2 - 192.168.10.12
switchport mode access
switchport access vlan 10
no shutdown

interface FastEthernet0/4
description Server1 - 192.168.10.100
switchport mode access
switchport access vlan 10
no shutdown
```
!Spanning tree
```text
spanning-tree mode pvst
```
!Enable all ports
```text
interface range FastEthernet0/1-24
no shutdown

end
write
```
---
## Switch 2

! SW2 config
```text
hostname SW2
no ip domain-lookup
```
!Create VLAN 20
```text
vlan 20
name Finance
```
!Router uplink
```text
interface FastEthernet0/1
description Uplink to R2 G0/0
switchport mode access
switchport access vlan 20
no shutdown
```
!PCs 
```text
interface FastEthernet0/2
description PC3 - 192.168.20.21
switchport mode access
switchport access vlan 20
no shutdown

interface FastEthernet0/3
description PC4 - 192.168.20.22
switchport mode access
switchport access vlan 20
no shutdown
```
!Spanning tree
```text
spanning-tree mode pvst
```
!Enable all ports
```text
interface FastEthernet0/1-24
no shutdown

end
write
```
---
## Switch 3

! SW3 config
```text
hostname SW3
no ip domain-lookup
```
!Create VLAN 30
```text
vlan 30
name Sales
```
!Router uplink
```text
interface FastEthernet0/1
description Uplink to R3 G0/0
switchport mode access
switchport access vlan 30
no shutdown
```
!PCs
```text
interface FastEthernet0/2
description PC5 - 192.168.30.31
switchport mode access
switchport access vlan 30
no shutdown

interface FastEthernet0/3
description PC6 - 192.168.30.32
switchport mode access
switchport access vlan 30
no shutdown
```
!Spanning Tree
```text
spanning-tree mode pvst
```
!Enable all ports
```text
interface FastEthernet0/1-24
no shutdown

end
write
```
---
## PC Configuraton

| Device      | Interface | IP Address     | Subnet Mask   | Default Gateway | VLAN | Notes                 |
| ----------- | --------- | -------------- | ------------- | --------------- | ---- | --------------------- |
| **PC1**     | NIC       | 192.168.10.11  | 255.255.255.0 | 192.168.10.1    | 10   | Connected to SW1 F0/2 |
| **PC2**     | NIC       | 192.168.10.12  | 255.255.255.0 | 192.168.10.1    | 10   | Connected to SW1 F0/3 |
| **PC3**     | NIC       | 192.168.20.21  | 255.255.255.0 | 192.168.20.1    | 20   | Connected to SW2 F0/2 |
| **PC4**     | NIC       | 192.168.20.22  | 255.255.255.0 | 192.168.20.1    | 20   | Connected to SW2 F0/3 |
| **PC5**     | NIC       | 192.168.30.31  | 255.255.255.0 | 192.168.30.1    | 30   | Connected to SW3 F0/2 |
| **PC6**     | NIC       | 192.168.30.32  | 255.255.255.0 | 192.168.30.1    | 30   | Connected to SW3 F0/3 |

---
## Server Configuration

| Device      | Interface | IP Address     | Subnet Mask   | Default Gateway | VLAN | Notes                 |
| ----------- | --------- | -------------- | ------------- | --------------- | ---- | --------------------- |
| **Server1** | NIC       | 192.168.10.100 | 255.255.255.0 | 192.168.10.1    | 10   | Connected to SW1 F0/4 |

---
## Verification

<img src="topology\topology_overview.png" alt="TOPOLOGY OVERVIEW" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">


## 1. VLAN Verification
- Verified VLANs on SW1, SW2, SW3. All access ports assigned correctly.
- 
**SW1**
<img src="screenshots\sw1_vlan.png" alt="SW1 VLAN" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**SW2**
<img src="screenshots\sw2_vlan.png" alt="SW2 VLAN" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**SW3**
<img src="screenshots\sw3_vlan.png" alt="SW3 VLAN" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">


## 2. MAC Address Table Verification
- Verified MAC addresses learned correctly on each switch.
- 
**Sw1**
<img src="screenshots\sw1_mac.png" alt="SW1 MAC" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**SW2**
<img src="screenshots\sw2_mac.png" alt="SW2 MAC" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**SW3**
<img src="screenshots\sw3_mac.png" alt="SW3 MAC" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## 3. Router Interfaces Verification
- All LAN and Serial interfaces are up with correct IPs.

**R1**
<img src="screenshots\r1_ip_int.png" alt="R1 IP INTERFACE" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**R2**
<img src="screenshots\r2_ip_int.png" alt="R2 IP INTERFACE" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**R3**
<img src="screenshots\r3_ip_int.png" alt="R3 IP INTERFACE" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## 4. OSPF Neighbor Verification
- R1 sees R2, R2 sees R1 & R3, R3 sees R2.

**R1**
<img src="screenshots\r1_ospf_neighbor.png" alt="R1 OSPF Neighbour" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**R2**
<img src="screenshots\r2_ospf_neighbor.png" alt="R2 OSPF Neighbour" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**R3**
<img src="screenshots\r3_ospf_neighbor.png" alt="R3 OSPF Neighbour" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## 5. OSPF Routing Table Verification
- All routers have OSPF learned routes to all LANs.

**R1**
<img src="screenshots\r1_ospf_route.png" alt="R1 OSPF Route" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**R2**
<img src="screenshots\r2_ospf_route.png" alt="R2 OSPF Route" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**R3**
<img src="screenshots\r3_ospf_route.png" alt="R3 OSPF Route" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## 6. Ping Tests

**PC1 Ping R1**
<img src="screenshots\pc1_ping_r1.png" alt="PC1 Ping R1" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC1 Ping PC3**
<img src="screenshots\pc1_ping_pc3.png" alt="PC1 Ping PC3" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC1 Ping PC5**
<img src="screenshots\pc1_ping_pc5.png" alt="PC1 Ping PC5" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC1 Ping Server**
<img src="screenshots\pc1_ping_server.png" alt="PC1 Ping SERVER" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC3 Ping R2**
<img src="screenshots\pc3_ping_r2.png" alt="PC3 Ping ROUTER2" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC3 Ping PC1**
<img src="screenshots\pc3_ping_pc1.png" alt="PC3 Ping PC1" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC3 Ping PC5**
<img src="screenshots\pc3_ping_pc5.png" alt="PC3 Ping PC5" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC5 ping R3**
<img src="screenshots\pc5_ping_r3.png" alt="PC5 Ping R3" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC5 Ping Server**
<img src="screenshots\pc5_ping_server.png" alt="PC5 Ping SERVER" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

**PC5 Ping PC3**
<img src="screenshots\pc5_ping_pc3.png" alt="PC5 Ping PC3" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">


## 7. Optional: Traceroute Verification

**PC6 to Server to verify path through routers.**
<img src="screenshots\pc6_traceroute_server.png" alt="PC6 TRACEROUTE SERVER" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

---
## Folder Structure

ospf-multi-router-office-network/
├── drawio/
│   └── topology.drawio          
│
├── lab-files/
│   └── project-3.pkt            # Packet Tracer lab file
│
├── pc-configs/
│   ├── PC config.txt
│   └── Server config.txt
│
├── router-configs/
│   ├── r1.cfg
│   ├── r2.cfg
│   └── r3.cfg
│
├── switch-configs/
│   ├── sw1.cfg
│   ├── sw2.cfg
│   └── sw3.cfg
│
├── screenshots/
│   ├── pc1_ping_pc3.png
│   ├── pc1_ping_pc5.png
│   ├── pc1_ping_r1.png
│   ├── pc1_ping_server.png
│   ├── pc_ping_pc1.png
│   └── pc3_ping_pc5.png
│   ├── pc3_ping_r2.png
│   ├── pc5_ping_pc3.png
│   ├── pc5_ping_r3.png
│   ├── pc5_ping_server.png
│   └── pc6_traceroute_server.png
│   ├── r1_ip_int.png
│   ├── r1_ospf_neighbor.png
│   ├── r1_ospf_route.png
│   ├── r2_ip_int.png
│   └── r2_ospf_neighbor.png
│   ├── r2_ospf_route.png
│   ├── r3_ip_int.png
│   ├── r3_ospf_neighbor.png
│   ├── r3_ospf_route.png
│   └── sw1_mac.png
│   ├── sw1_vlan.png
│   ├── sw2_mac.png
│   ├── sw2_vlan.png
│   ├── sw3_mac.png
│   └── sw3_vlan.png
│
├── topology/
│   └── topology.png
│
├── README.md
├── verification.md
└── LICENSE

---
