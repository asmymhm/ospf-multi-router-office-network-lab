# 🏢 OSPF Multi-Router Office Network
---
Table of Contents

1.  [📘 Project Overview](#-project-overview)
2.  [🎯 Project Objectives](#-project-objectives)
3.  [🌐 Network Topology](#-network-topology)
4.  [🗂️ Device Interface Table](#-device-interface-table)
5.  [📝 IP Addressing Table](#-ip-addressing-table)
6.  [🛠️ Lab Steps](#-lab-steps)
7.  [💻 Device Configuration](#-device-configuration)
	- [🚦 Router 1](#-router-1)
	- [🚦 Router 2](#-router-2)
	- [🚦 Router 3](#-router-3)
	- [🔀 Switch 1](#-switch-1)
	- [🔀 Switch 2](#-switch-2)
	- [🔀 Switch 3](#-switch-3)
	- [🖥️ PC & Server Configuration](#-pc-and-server-configuration)
8.  [✅ Verification](#verification)
9.  [⚡ How to Run Lab](#-how-to-run-lab)
10. [📂 Folder Structure](#-folder-structure)
11. [🎓 Learning Outcomes](#-learning-outcomes)
12. [ℹ️ Repository Info](#-repository-info)

---
## 📘 Project overview

Implement OSPF across multiple routers to demonstrate dynamic routing in an enterprise environment. 
The lab uses Packet Tracer.

---
## 🎯 Project Objective

- Configure OSPF area 0 across 3 routers.
- Configure serial WAN links (/30) between routers.
- Configure LANs for 3 sites with PCs and a server.
- Verify OSPF adjacency, routing, and end-to-end connectivity.

---
## 🌐 Network Topology
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
## 🗂️ Device Interface table

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
## 📝 IP addressing 

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
## 🛠️ Lab steps

1. Build topology in Packet Tracer (place devices & connect cables).
2. Configure PCs and Server (static IPs).
3. Configure switches .
4. Configure routers interfaces and set clock rate on DCE serials.
5. Configure OSPF (area 0) on all routers.
6. Verify OSPF neighbors and learned routes.
7. Test end-to-end pings (PC to PC, PC to Server).
8. Capture screenshots.

---
## 💻 Device Configuration

## 🚦 Router 1 

```text
interface GigabitEthernet0/0
description LAN to SW1
ip address 192.168.10.1 255.255.255.0
no shutdown
```
[View Full Configuration File →](router-configs/r1.cfg)

---
## 🚦 Router 2

```text
interface GigabitEthernet0/0
description LAN to SW2
ip address 192.168.20.1 255.255.255.0
no shutdown
```
[View Full Configuration File →](router-configs/r2.cfg)
---
## 🚦 Router 3

```text
interface GigabitEthernet0/0
description LAN to SW3
ip address 192.168.30.1 255.255.255.0
no shutdown
```
[View Full Configuration File →](router-configs/r3.cfg)
---

## 🔀 Switch 1

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
[View Full Configuration File →](switch-configs/sw1.cfg)
---
## 🔀 Switch 2

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
[View Full Configuration File →](switch-configs/sw2.cfg)
---
## 🔀 Switch 3

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
[View Full Configuration File →](switch-configs/sw3.cfg)
---
## 🖥️ PC & Server Configuration

| Device      | Interface | IP Address     | Subnet Mask   | Default Gateway | VLAN | Notes                 |
| ----------- | --------- | -------------- | ------------- | --------------- | ---- | --------------------- |
| **PC1**     | NIC       | 192.168.10.11  | 255.255.255.0 | 192.168.10.1    | 10   | Connected to SW1 F0/2 |
| **PC2**     | NIC       | 192.168.10.12  | 255.255.255.0 | 192.168.10.1    | 10   | Connected to SW1 F0/3 |

Refer to full files: 
[PC Configs →](pc-configs/pc-configs.txt)


| **Server1** | NIC       | 192.168.10.100 | 255.255.255.0 | 192.168.10.1    | 10   | Connected to SW1 F0/4 |

Refer to full files: 
[SERVER Configs →](pc-configs/server-configs.txt)

---
## ✅ Verification

🗺️ Topology Screenshot 

shows the full lab layout in Packet Tracer.
<img src="topology/topology_overview.png" alt="TOPOLOGY OVERVIEW" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">


## 1. 🔍 VLAN Verification
- Verified VLANs on SW1, SW2, SW3. All access ports assigned correctly.
- 
🔀**SW1**

<img src="screenshots/sw1_vlan.png" alt="SW1 VLAN" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

🔀**SW2**

<img src="screenshots/sw2_vlan.png" alt="SW2 VLAN" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

🔀**SW3**

<img src="screenshots/sw3_vlan.png" alt="SW3 VLAN" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">


## 2. 🖧 MAC Address Table Verification

- Verified MAC addresses learned correctly on each switch.

🔀**Sw1**

<img src="screenshots/sw1_mac.png" alt="SW1 MAC" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

🔀**SW2**

<img src="screenshots/sw2_mac.png" alt="SW2 MAC" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

🔀**SW3**

<img src="screenshots/sw3_mac.png" alt="SW3 MAC" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## 3. 🚦 Router Interfaces Verification

- All LAN and Serial interfaces are up with correct IPs.

🚦**R1**

<img src="screenshots/r1_ip_int.png" alt="R1 IP INTERFACE" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

🚦**R2**

<img src="screenshots/r2_ip_int.png" alt="R2 IP INTERFACE" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

🚦**R3**

<img src="screenshots/r3_ip_int.png" alt="R3 IP INTERFACE" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## 4. 🤝 OSPF Neighbor Verification

- R1 sees R2, R2 sees R1 & R3, R3 sees R2.

🚦**R1**

<img src="screenshots/r1_ospf_neighbor.png" alt="R1 OSPF Neighbour" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

🚦**R2**

<img src="screenshots/r2_ospf_neighbor.png" alt="R2 OSPF Neighbour" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

🚦**R3**

<img src="screenshots/r3_ospf_neighbor.png" alt="R3 OSPF Neighbour" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## 5. 🗺️ OSPF Routing Table Verification

- All routers have OSPF learned routes to all LANs.

🚦**R1**

<img src="screenshots/r1_ospf_route.png" alt="R1 OSPF Route" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

🚦**R2**

<img src="screenshots/r2_ospf_route.png" alt="R2 OSPF Route" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

🚦**R3**

<img src="screenshots/r3_ospf_route.png" alt="R3 OSPF Route" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

## 6. 📶 Ping Tests

📶**PC1 Ping R1**

<img src="screenshots/pc1_ping_r1.png" alt="PC1 Ping R1" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

📶**PC1 Ping PC3**

<img src="screenshots/pc1_ping_pc3.png" alt="PC1 Ping PC3" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

📶**PC1 Ping PC5**

<img src="screenshots/pc1_ping_pc5.png" alt="PC1 Ping PC5" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

📶**PC1 Ping Server**

<img src="screenshots/pc1_ping_server.png" alt="PC1 Ping SERVER" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

📶**PC3 Ping R2**

<img src="screenshots/pc3_ping_r2.png" alt="PC3 Ping ROUTER2" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

📶**PC3 Ping PC1**

<img src="screenshots/pc3_ping_pc1.png" alt="PC3 Ping PC1" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

📶**PC3 Ping PC5**

<img src="screenshots/pc3_ping_pc5.png" alt="PC3 Ping PC5" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

📶**PC5 ping R3**

<img src="screenshots/pc5_ping_r3.png" alt="PC5 Ping R3" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

📶**PC5 Ping Server**

<img src="screenshots/pc5_ping_server.png" alt="PC5 Ping SERVER" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

📶**PC5 Ping PC3**

<img src="screenshots/pc5_ping_pc3.png" alt="PC5 Ping PC3" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">


## 7. ✨ Optional: Traceroute Verification

📶**PC6 to Server to verify path through routers.**

<img src="screenshots/pc6_traceroute_server.png" alt="PC6 TRACEROUTE SERVER" style="border:1px solid #ddd; padding:5px; max-width:100%; height:auto;">

---

## ⚡ How to Run Lab

1. **Open the lab file**

   - Open Packet Tracer and load the lab file located at:
     lab-files/ospf-multi-router-office-network.pkt

2. **Verify device connectivity**

   - Check that all PCs, switches, and routers are powered on and connected correctly.

3. **Apply configurations (if needed)**

   - Router configurations: router-configs/r1.cfg, r2.cfg, r3.cfg

   - Switch configurations: switch-configs/sw1.cfg, sw2.cfg, sw3.cfg

   - PC and Server configurations: pc-configs/pc-configs.txt, server-configs.txt

4. **Follow lab steps**

   - Configure OSPF area 0 across all routers

   - Verify VLAN assignments and switch connectivity

   - Test end-to-end connectivity using ping and traceroute

5. **verification screenshots**

   - Save screenshots of OSPF neighbors, routing tables, VLAN tables, and ping results for documentation

---

## 📂 Folder Structure

ospf-multi-router-office-network/
├── drawio/
│   └── topology.drawio          
│
├── lab-files/
│   └── ospf-multi-router-office-network.pkt            
│
├── pc-configs/
│   ├── pc-configs.txt
│   └── server-configs.txt
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

## 🎓 Learning Outcomes

By completing this lab, you will be able to:

- Configure **OSPF dynamic routing** across multiple routers in an enterprise network

- Assign and verify **VLANs** and switch port configurations

- Configure router **serial WAN links (/30)** for point-to-point connectivity

- Verify **routing tables** and **OSPF neighbor relationships**

- Perform **end-to-end connectivity tests** using ping and traceroute

- Understand **device interface management** and subnetting for small office networks

- Use **Cisco Packet Tracer** for practical network simulation and documentation

---

### 📂 Repository Info
This project is part of my **CCNA Lab Portfolio**.  
Explore more labs here 👉 [@asmymhm](https://github.com/asmymhm)

---
