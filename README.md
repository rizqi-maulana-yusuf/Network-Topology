# 🏢 Enterprise Multi-Site Network with VLANs, Voice & Wireless Integration

> A fully routed, multi-site enterprise network infrastructure simulation demonstrating advanced VLAN segmentation, Router-on-a-Stick inter-VLAN routing, wireless integration, and VoIP telephony services using Cisco Packet Tracer.

This project showcases intermediate-to-advanced network engineering capabilities, focusing on converged networks where voice, wireless data, and wired office systems coexist within a structured multi-site topology.

---

## 🏗️ Network Architecture & Topology

<img width="1095" height="338" alt="{10C3B509-A6C1-4C69-9E06-E12E61615428}" src="https://github.com/user-attachments/assets/ae39131c-2992-4793-9b22-d3ad4959c8bd" />


The infrastructure is strategically segmented into multi-departmental VLANs across two main sites connected via a point-to-point inter-router backbone link. IP addressing utilizes a hybrid allocation method (Dynamic DHCP for mobile/voice endpoints and Static allocation for fixed critical infrastructure).

### IP Addressing & VLAN Table
| Site / Segment | VLAN ID | Subnet / Network | Allocation Method | Description & Purpose |
| :--- | :---: | :--- | :---: | :--- |
| **Site A - Data** | VLAN 10 | `192.33.10.0/24` | Static | Wired client workstations (PC0, PC1)[cite: 7] |
| **Site A - Wireless** | VLAN 20 | `192.33.20.0/24` | DHCP | Smartphones connected via Access Point[cite: 7] |
| **Site B - Voice** | VLAN 30 | `192.33.30.0/24` | DHCP | Cisco 7960 IP Phones (Option 150 TFTP)[cite: 7] |
| **Site B - Printer/Data** | VLAN 40 | `192.33.40.0/24` | Static | Network Printers and local workstations[cite: 7] |
| **Inter-Router Link** | N/A | `10.20.33.0/30` | Static | Backbone link connecting Router0 & Router1[cite: 7] |

---

## 🚀 How to Run This Lab

To test and explore this multi-site converged network:
1. Ensure **Cisco Packet Tracer** (v8.0 or newer) is installed.
2. Download the `.pkt` file from this repository.
3. Open the file in Packet Tracer and allow Spanning Tree Protocol (STP) to converge (ensure all link lights turn green).
4. Inspect the DHCP status on the Smartphones and IP Phones, or test calls and ping connectivity using the steps below.

---

## ⚙️ Full Configuration Commands

### 1. Router 0 (Site A) - Inter-VLAN Routing & DHCP Pools
```text
! --- Inter-VLAN Routing (Router-on-a-Stick) ---
Router(config)# interface FastEthernet 0/1.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.33.10.1 255.255.255.0

Router(config)# interface FastEthernet 0/1.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.33.20.1 255.255.255.0

! --- DHCP Pool for Wireless Smartphones (VLAN 20) ---
Router(config)# ip dhcp pool POOL_WIRELESS_HP
Router(dhcp-config)# network 192.33.20.0 255.255.255.0
Router(dhcp-config)# default-router 192.33.20.1

! --- Inter-Router Backbone Interface ---
Router(config)# interface FastEthernet 0/0
Router(config-if)# ip address 10.20.33.1 255.255.255.252
Router(config-if)# no shutdown
```

### 2. Router 1 (Site B) - Inter-VLAN Routing, VoIP & Telephony Service
```text
! --- Inter-VLAN Routing (Router-on-a-Stick) ---
Router(config)# interface FastEthernet 0/1.30
Router(config-subif)# encapsulation dot1Q 30
Router(config-subif)# ip address 192.33.30.1 255.255.255.0

Router(config)# interface FastEthernet 0/1.40
Router(config-subif)# encapsulation dot1Q 40
Router(config-subif)# ip address 192.33.40.1 255.255.255.0

! --- DHCP Pool for Cisco IP Phones (VLAN 30 with Option 150) ---
Router(config)# ip dhcp pool VOICE_POOL
Router(dhcp-config)# network 192.33.30.0 255.255.255.0
Router(dhcp-config)# default-router 192.33.30.1
Router(dhcp-config)# option 150 ip 192.33.30.1

! --- Cisco CallManager Express / Telephony Service ---
Router(config)# telephony-service
Router(config-telephony)# max-ephones 5
Router(config-telephony)# max-dn 5
Router(config-telephony)# ip source-address 192.33.30.1 port 2000
Router(config-telephony)# auto assign 1 to 5

! --- Inter-Router Backbone Interface ---
Router(config)# interface FastEthernet 0/0
Router(config-if)# ip address 10.20.33.2 255.255.255.252
Router(config-if)# no shutdown
```

---

## 🧪 Verification & Test Results

The following validations confirmed correct operation:
- [x] **Dynamic IP Assignment:** Verified that Smartphones (via Wireless Access Point) and Cisco IP Phones successfully pull IP addresses dynamically from their respective router DHCP pools[cite: 7].
- [x] **Static Endpoint Reachability:** Workstations and Network Printers configured with static addresses respond accurately to ICMP queries[cite: 7].
- [x] **VoIP Registration:** Cisco IP Phones completed SCCP registration with the onboard *telephony-service* and established active voice channels[cite: 7].
- [x] **Cross-Site Routing:** Validated end-to-end communication from Site A clients (PCs and Smartphones) to Site B endpoints (IP Phones and Printers) across the `10.20.33.0/30` backbone[cite: 7].

---

## 💡 Lessons Learned & Troubleshooting
* **Option 150 Significance:** During VoIP setup, IP phones failed to download configuration files until **Option 150** was properly declared in the DHCP pool, pointing directly to the router's TFTP IP address (`192.33.30.1`).
* **Trunk Port Encapsulation:** Ensuring proper 802.1Q encapsulation mapping on router sub-interfaces (`dot1Q [vlan_id]`) was critical to prevent tagging mismatches between the Cisco switches and routers.
