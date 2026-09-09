Enterprise Multi-Site Network with VLANs, Voice & Wireless Integration

## 📌 Project Overview
This project demonstrates a fully routed, multi-site enterprise network infrastructure engineered in Cisco Packet Tracer. The architecture implements full VLAN segmentation across all departments, Inter-VLAN Routing via router sub-interfaces, and Inter-Router Routing between site gateways. 

IP addressing is strategically allocated using Hybrid Addressing (DHCP + Static):
- Dynamic (DHCP): Managed automatically for highly mobile devices (Smartphones via Access Point) and VoIP infrastructure (IP Phones with Option 150).
- Static IP: Assigned manually to fixed endpoints (Workstations and Network Printers) for predictability and administrative control.

---

## 📐 Network Segmentation & Addressing Plan

| Site / Segment | VLAN ID | Subnet / Network | Allocation Method | Description & Purpose |
| :--- | :---: | :--- | :---: | :--- |
| Site A - Data | VLAN 10 | 192.33.10.0/24 | Static | Wired client workstations (PC0, PC1) |
| Site A - Wireless (HP) | VLAN 20 | 192.33.20.0/24 | DHCP | Smartphones connected via Access Point |
| Site B - Voice (VoIP) | VLAN 30 | 192.33.30.0/24 | DHCP | Cisco 7960 IP Phones (Option 150 TFTP) |
| Site B - Printer & Data | VLAN 40 | 192.33.40.0/24 | Static | Network Printers and local workstations |
| Inter-Router Link | N/A | 10.20.33.0/30 | Static | Backbone link connecting Router0 & Router1 |

---

## 🖼️ Network Topology Diagram

<img width="972" height="315" alt="{912CD05B-BFBA-41B4-9761-3573398E8C1A}" src="https://github.com/user-attachments/assets/a4a5151b-9b5f-4d46-b083-2c210ed03d86" />

---

## ⚙️ Core Configuration Highlights

### 1. Inter-VLAN Routing Setup (Router-on-a-Stick)
All VLAN traffic is trunked to router sub-interfaces to enable communication across all segmented networks:

! Router0 - Site A Inter-VLAN Routing
interface FastEthernet 0/1.10
 encapsulation dot1Q 10
 ip address 192.33.10.1 255.255.255.0

interface FastEthernet 0/1.20
 encapsulation dot1Q 20
 ip address 192.33.20.1 255.255.255.0

! Router1 - Site B Inter-VLAN Routing
interface FastEthernet 0/1.30
 encapsulation dot1Q 30
 ip address 192.33.30.1 255.255.255.0

interface FastEthernet 0/1.40
 encapsulation dot1Q 40
 ip address 192.33.40.1 255.255.255.0

### 2. Selective DHCP Pools (Smartphones & IP Phones Only)
DHCP services are configured exclusively for mobile wireless endpoints and IP telephony:

! Router0: DHCP Pool for Wireless Smartphones (VLAN 20)
ip dhcp pool POOL_WIRELESS_HP
 network 192.33.20.0 255.255.255.0
 default-router 192.33.20.1

! Router1: DHCP Pool for Cisco IP Phones (VLAN 30)
ip dhcp pool VOICE_POOL
 network 192.33.30.0 255.255.255.0
 default-router 192.33.30.1
 option 150 ip 192.33.30.1

### 3. Voice Telephony Service Activation
telephony-service
 max-ephones 5
 max-dn 5
 ip source-address 192.33.30.1 port 2000
 auto assign 1 to 5

### 4. Inter-Router Backbone Routing
Full routing configured across the 10.20.33.0/30 link to route traffic between Site A (VLAN 10, 20) and Site B (VLAN 30, 40).

---

## ✅ Verification & Test Results
- [x] Dynamic IP Assignment: Confirmed Smartphones (Access Point) and IP Phones dynamically acquire IP addresses via DHCP.
- [x] Static Endpoint Reachability: PCs and Network Printers configured with static IPs respond to ICMP ping queries across local gateways.
- [x] VoIP Registration: IP Phones complete registration with Telephony Service and establish voice channels.
- [x] Full Cross-Site Routing: Verified 100% ICMP reachability from Site A endpoints (PC/Smartphone) to Site B endpoints (VoIP/Printer) through the inter-router connection.
