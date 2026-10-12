# 🔐 Campus Secure Network

### Enterprise VLAN Segmentation • Centralized DHCP • Secure Network Infrastructure

> **Enterprise Network Simulation | EVE-NG | IPv4 | VLANs | 802.1Q | DHCP | Network Security**

<div align="center">

**3 Switches** • **1 DHCP Server** • **8 Endpoints** • **6 VLANs** • **6 Departmental Networks**

</div>

---





## 🧭 Navigation

- [🎯 Project Objective](#-1-project-objective)
- [🖥️ Network Topology](#network-topology)
- [🖧 Device Inventory](#-3-device-inventory)
- [🔢 IP Addressing Plan](#-4-ip-addressing-plan)
- [🔌 Interface Mapping](#-5-interface-mapping)
- [🛠️ Technologies Used](#technologies-used)
- [🛠️ Project Phases](#project-phases)
  - [Phase 1 — VLANs and Interface Configuration](#-phase-1--interface-configuration-vlans-and-switching)
  - [Phase 2 — Centralized DHCP and Router-on-a-Stick](#-phase-2--centralized-dhcp-configuration-using-router-on-a-stick)
  - [Phase 3 — ACLs and Inter-VLAN Traffic Security](#-phase-3--access-control-lists-acls-and-inter-vlan-traffic-security)
  - [Phase 4 — DTP Security](#-phase-4--disable-dtp-negotiation-using-switchport-nonegotiate)
  - [Phase 5 — DHCP Snooping, DAI and Rate Limiting](#-phase-5--dhcp-snooping-dynamic-arp-inspection-dai-and-dhcp-rate-limiting)
  - [Phase 6 — STP Security](#-phase-6--spanning-tree-protocol-stp-security-portfast-bpdu-guard-and-root-guard)
- [🔄 Implementation Roadmap](#-7-project-implementation-roadmap)
- [🧪 Verification and Testing](#-8-verification-and-testing)
- [🏆 Project Outcomes](#-9-project-outcomes)
- [👨‍💻 Author](#author)

---

## 🎯 1. Project Objective

Design and implement a simulated enterprise campus network representing a small company's office environment with multiple floors and departments using EVE-NG and Cisco IOS. The project focuses on VLAN-based network segmentation, access and trunk port configuration, inter-VLAN routing, centralized DHCP services, and DHCP relay to enable dynamic IP address allocation across multiple subnets. It also incorporates DHCP Snooping to protect against unauthorized DHCP servers, SSH-only remote management to secure administrative access, and network connectivity verification using Cisco IOS diagnostic commands. The overall objective is to build a structured, reliable, and secure campus network while gaining practical experience in enterprise networking, network troubleshooting, and network security.



<a id="network-topology"></a>

## 🖥️ 2. Network Topology

### 🏢 Campus Network Architecture

The network is organized into three main switching components:

1. **MAIN_SWITCH** — Central switching device.
2. **SW_FLR1** — First-floor departmental switch.
3. **SW_GROUND_FLR** — Ground-floor departmental switch.

The floor switches connect departmental endpoints to the network. Trunk links transport the required VLANs between switches.

The DHCP server connects to the central switching infrastructure to provide DHCP services.

### 📸 EVE-NG Topology

<img width="1607" height="857" alt="Screenshot 2026-10-10 021307" src="https://github.com/user-attachments/assets/f096a1fe-21e0-4ed8-a22e-97489766acff" />

---

# 🖧 3. Device Inventory

## 🔹 Switching Infrastructure

| No. | Device Name | Role | Primary Responsibility |
|---:|---|---|---|
| 1 | `MAIN_SWITCH` | Central switch | VLAN interfaces, central switching, DHCP relay |
| 2 | `SW_FLR1` | First-floor switch | Departmental endpoint connectivity |
| 3 | `SW_GROUND_FLR` | Ground-floor switch | Departmental endpoint connectivity |

## 🔹 Server Infrastructure

| No. | Device Name | Role | IP Address |
|---:|---|---|---|
| 1 | `DHCP_SERVER` | Central DHCP server | `10.10.10.100` |

## 🔹 End Devices

| No. | Device Name | Department | VLAN |
|---:|---|---|---:|
| 1 | `VPC1_VLAN10_IT` | IT | 10 |
| 2 | `VPC2_VLAN20_HR` | HR | 20 |
| 3 | `VPC3_VLAN30_MGMT` | Management | 30 |
| 4 | `VPC2_cyber_VLAN40` | Cybersecurity | 40 |
| 5 | `VPC4_VLAN40_Cyber` | Cybersecurity | 40 |
| 6 | `VPC5_VLAN50_FINANCE` | Finance | 50 |
| 7 | `VPC2_IT_VLAN10` | IT | 10 |
| 8 | `VPC6_VLAN60_MANAGER` | Manager | 60 |

---

# 🔢 4. IP Addressing Plan

## 💻 4.1 End-Device IP Addressing

| Device Name | VLAN | IP Address | Subnet Mask | Default Gateway |
|---|---:|---|---|---|
| `VPC1_VLAN10_IT` | 10 | `10.10.10.2` | `255.255.255.0` | `10.10.10.1` |
| `VPC2_VLAN20_HR` | 20 | `10.10.20.2` | `255.255.255.0` | `10.10.20.1` |
| `VPC3_VLAN30_MGMT` | 30 | `10.10.30.2` | `255.255.255.0` | `10.10.30.1` |
| `VPC2_cyber_VLAN40` | 40 | `10.10.40.3` | `255.255.255.0` | `10.10.40.1` |
| `VPC4_VLAN40_Cyber` | 40 | `10.10.40.4` | `255.255.255.0` | `10.10.40.1` |
| `VPC5_VLAN50_FINANCE` | 50 | `10.10.50.2` | `255.255.255.0` | `10.10.50.1` |
| `VPC2_IT_VLAN10` | 10 | `10.10.10.3` | `255.255.255.0` | `10.10.10.1` |
| `VPC6_VLAN60_MANAGER` | 60 | `10.10.60.3` | `255.255.255.0` | `10.10.60.1` |

## 📡 4.2 DHCP Server Addressing

| Parameter | Configuration |
|---|---|
| Device Name | `DHCP_SERVER` |
| Server IP Address | `10.10.10.100` |
| Connected Network | VLAN 10 / IT |
| DHCP Relay Device | `MAIN_SWITCH` |
| Helper Address | `10.10.10.100` |



---

# 🔌 5. Interface Mapping


## 🔹 5.1 MAIN_SWITCH

| Local Interface | Connected Device | Remote Interface | Purpose |
|---|---|---|---|
| `Gi0/0` | `DHCP_SERVER` | `Gi0/0` | DHCP server connectivity |
| `Gi0/1` | `SW_FLR1` | Verify in EVE-NG | First-floor uplink |
| `Gi1/1` | `SW_GROUND_FLR` | Verify in EVE-NG | Ground-floor uplink |

## 🔹 5.2 SW_FLR1

| Local Interface | Connected Device | Purpose |
|---|---|---|
| `Gi0/1` | `VPC1_VLAN10_IT` | IT endpoint |
| `Gi0/2` | `VPC2_VLAN20_HR` | HR endpoint |
| `Gi0/3` | `VPC3_VLAN30_MGMT` | Management endpoint |
| `Gi1/0` | `VPC2_cyber_VLAN40` | Cybersecurity endpoint |
| Uplink | `MAIN_SWITCH` | Trunk link; verify interface |

## 🔹 5.3 SW_GROUND_FLR

| Local Interface | Connected Device | Purpose |
|---|---|---|
| `Gi0/0` | `VPC2_IT_VLAN10` | IT endpoint |
| `Gi0/1` | `VPC4_VLAN40_Cyber` | Cybersecurity endpoint |
| `Gi0/2` | `VPC5_VLAN50_FINANCE` | Finance endpoint |
| `Gi0/3` | `VPC6_VLAN60_MANAGER` | Manager endpoint |
| Uplink | `MAIN_SWITCH` | Trunk link; verify interface |

### 🔍 Interface Verification Commands

```cisco
show ip interface brief
show interfaces status
show interfaces trunk
show cdp neighbors
show running-config
```

---

<a id="technologies-used"></a>

## 🛠️ 6. Technologies Used

| Technology | Purpose |
|---|---|
| EVE-NG | Network emulation |
| Cisco IOS | Device configuration and troubleshooting |
| IPv4 | Network-layer addressing |
| VLANs | Departmental segmentation |
| IEEE 802.1Q | VLAN tagging across trunk links |
| Access Ports | Endpoint connectivity |
| Trunk Ports | Multi-VLAN connectivity between switches |
| VLAN Interfaces | VLAN gateway configuration |
| DHCP | Dynamic address allocation |
| DHCP Relay | DHCP forwarding across subnets |
| DHCP Snooping | DHCP server protection |
| SSH Version 2 | Encrypted remote management |
| NTP | Network time synchronization |
| Wireshark | Packet capture and protocol analysis |
| Cisco IOS CLI | Configuration and verification |

---

<a id="project-phases"></a>

## 🛠️ 7. Project Phases


# 🔹 Phase 1 — Interface Configuration, VLANs and Switching

## 🎯 Objective

Configure all required VLANs, assign VLAN names, configure access and trunk ports, and implement DHCP relay on the central switch. This phase focuses on establishing departmental network segmentation and connectivity between switches. The implementation is divided into three tasks corresponding to the switches in the topology.

---

## 📍 1.1 SW_FLR1 — First-Floor Switch Configuration

### 🎯 Objective

Configure the first-floor switch to provide network connectivity to IT, HR, Management, and Cybersecurity endpoints using VLAN segmentation, access ports, and trunk connectivity toward `MAIN_SWITCH`.

### ⚙️ Tasks to Perform

1. Create VLANs 10, 20, 30, 40, 50, and 60.
2. Assign the correct VLAN names.
3. Configure access interfaces for connected endpoints.
4. Configure the uplink toward `MAIN_SWITCH` as a trunk.
5. Allow VLANs 10, 20, 30, 40, 50, and 60 on the trunk.
6. Configure DHCP Snooping where supported.
7. Configure trusted interfaces appropriately.
8. Verify VLAN membership, interface status, and trunk operation.

### 🗂️ VLAN Configuration Plan

| VLAN ID | VLAN Name |
|---:|---|
| 10 | IT |
| 20 | HR |
| 30 | MGMT |
| 40 | CYBER |
| 50 | FINANCE |
| 60 | MANAGER |

### 🔌 Interface Configuration Plan

| Interface | Connected Endpoint | VLAN / Mode |
|---|---|---|
| `Gi0/1` | `VPC1_VLAN10_IT` | VLAN 10 — Access |
| `Gi0/2` | `VPC2_VLAN20_HR` | VLAN 20 — Access |
| `Gi0/3` | `VPC3_VLAN30_MGMT` | VLAN 30 — Access |
| `Gi1/0` | `VPC2_cyber_VLAN40` | VLAN 40 — Access |
| Uplink | `MAIN_SWITCH` | Trunk — Multiple VLANs |

### 📸 Interface Configuration

<img width="1757" height="623" alt="SW_FLR1 Interface Configuration" src="https://github.com/user-attachments/assets/d8219e66-7644-4280-a8d4-11ca4457fedd" />

<br>

### 🔗 Trunk Port Configuration

<img width="1727" height="761" alt="SW_FLR1 Trunk Configuration" src="https://github.com/user-attachments/assets/a2fc3ecd-f70e-4683-900a-018bf7738e19" />

<br>

### 🔍 Verification

```cisco
show vlan brief
show interfaces trunk
```



### 📸 VLAN and Trunk Verification

<img width="1382" height="482" alt="SW_FLR1 VLAN and Trunk Verification" src="https://github.com/user-attachments/assets/59d431db-34c2-4f52-b33f-21198475c9d5" />

<br>





---


## 📍 1.2 SW_GROUND_FLR — Ground-Floor Switch Configuration

### 🎯 Objective

Configure the ground-floor switch to provide network connectivity to IT, Cybersecurity, Finance, and Manager endpoints.

### ⚙️ Tasks to Perform

1. Create the six departmental VLANs.
2. Configure the correct VLAN names.
3. Assign access ports to their intended VLANs.
4. Configure the uplink toward `MAIN_SWITCH` as a trunk.
5. Allow the required VLANs across the trunk.
6. Configure DHCP Snooping where supported.
7. Verify interface status and VLAN assignments.
8. Test connectivity between endpoints and their gateways.

### 🔌 Interface Configuration Plan

| Interface | Connected Endpoint | VLAN |
|---|---|---:|
| `Gi0/0` | `VPC2_IT_VLAN10` | 10 |
| `Gi0/1` | `VPC4_VLAN40_Cyber` | 40 |
| `Gi0/2` | `VPC5_VLAN50_FINANCE` | 50 |
| `Gi0/3` | `VPC6_VLAN60_MANAGER` | 60 |
| Uplink | `MAIN_SWITCH` | Trunk |

### 📸 Interface Configuration

<img width="1615" height="652" alt="Screenshot 2026-10-09 213227" src="https://github.com/user-attachments/assets/af983fe3-aec2-479f-a54f-615f9e509635" />


<br>

### 🔗 Trunk Port Configuration

<img width="1322" height="568" alt="Screenshot 2026-10-09 213237" src="https://github.com/user-attachments/assets/2d2a3ca9-d2f0-4aba-8523-e9070424bacf" />


<br>


### 🔍 Verification

```cisco
show vlan brief
show interfaces trunk
```
<br>
<img width="1702" height="645" alt="Screenshot 2026-10-09 213308" src="https://github.com/user-attachments/assets/a9a721fb-289e-4b68-a6bb-3e96dc467fc8" />

<br>


---

## 📍 1.3 MAIN_SWITCH — Central Switch Configuration

### 🎯 Objective

Configure the central switching device to provide VLAN gateway interfaces, inter-VLAN routing where supported, trunk connectivity, and DHCP relay.

### ⚙️ Tasks to Perform

1. Create VLANs 10, 20, 30, 40, 50, and 60.
2. Configure the departmental VLAN names.
3. Configure trunk links to `SW_FLR1` and `SW_GROUND_FLR`.
4. Allow the required VLANs on the trunk links.
5. Configure VLAN interfaces using the planned gateway addresses.
6. Configure DHCP relay on the relevant client VLAN interfaces.
7. Configure DHCP Snooping and appropriate trusted interfaces.
8. Verify the VLAN interfaces and trunk links.
9. Test DHCP allocation and endpoint connectivity.

### 🌐 VLAN Interface Addressing

| VLAN Interface | VLAN Name | IP Address |
|---|---|---|
| `Vlan10` | IT | `10.10.10.1/24` |
| `Vlan20` | HR | `10.10.20.1/24` |
| `Vlan30` | MGMT | `10.10.30.1/24` |
| `Vlan40` | CYBER | `10.10.40.1/24` |
| `Vlan50` | FINANCE | `10.10.50.1/24` |
| `Vlan60` | MANAGER | `10.10.60.1/24` |

### 📡 DHCP Relay Configuration

The centralized DHCP server is configured with the address:

`10.10.10.100`

Example relay configuration:

```cisco
interface Vlan20
 ip helper-address 10.10.10.100
```

Apply the helper address to each relevant client VLAN interface that requires DHCP relay.

A helper address is not necessarily required on VLAN 10 because the DHCP server is directly connected to the same subnet in this design.

### 📸 Interface Configuration

<img width="1672" height="605" alt="Screenshot 2026-10-09 214309" src="https://github.com/user-attachments/assets/0b7cdcb3-cc81-4265-ab21-f683e40aee1c" />



<br>
<img width="1531" height="783" alt="Screenshot 2026-10-09 214317" src="https://github.com/user-attachments/assets/eba7a53d-91f5-48db-9c25-6170b804b171" />
<br>


### 🔗 Trunk Port Configuration

<br>
<img width="1326" height="425" alt="Screenshot 2026-10-09 214324" src="https://github.com/user-attachments/assets/6059ffb5-27ba-4e86-a840-fe9f67fc8541" />

<br>


<br>
### 🔍 Verification

```cisco
show vlan brief
show interfaces trunk
```
<img width="1191" height="752" alt="Screenshot 2026-10-09 214340" src="https://github.com/user-attachments/assets/b0097e8e-5e58-49fc-910a-300ce36fda58" />

---



# 🔹 Phase 2 — Centralized DHCP Configuration Using Router-on-a-Stick

## 🎯 Objective

Configure a centralized DHCP server using the Router-on-a-Stick architecture to provide dynamic IP address allocation to multiple departmental VLANs. Configure IEEE 802.1Q subinterfaces for VLANs 10, 20, 30, 40, 50, and 60 on the `DHCP` router, create DHCP pools for each subnet, and assign the appropriate default gateway to each pool. Finally, verify DHCP address allocation and test connectivity between endpoints within the same VLAN and across different VLANs.

---

## 📍 2.1 DHCP Router — Physical Interface and Subinterface Configuration

### 🎯 Objective

Configure the physical interface `GigabitEthernet0/0` as the trunk-connected interface and create six logical subinterfaces to support the departmental VLANs.

### ⚙️ Tasks to Perform

1. Configure the hostname as `DHCP`.
2. Enable the physical interface `GigabitEthernet0/0`.
3. Create subinterfaces for VLANs 10, 20, 30, 40, 50, and 60.
4. Configure IEEE 802.1Q encapsulation on each subinterface.
5. Assign the appropriate IP address to each subinterface.
6. Verify the interface and subinterface status.

### 🔌 Interface Configuration Plan

| Physical Interface | Subinterface | VLAN ID | Subinterface IP Address | Subnet Mask |
|---|---|---:|---|---|
| `Gi0/0` | `Gi0/0.10` | 10 | `10.10.10.100` | `255.255.255.0` |
| `Gi0/0` | `Gi0/0.20` | 20 | `10.10.20.100` | `255.255.255.0` |
| `Gi0/0` | `Gi0/0.30` | 30 | `10.10.30.100` | `255.255.255.0` |
| `Gi0/0` | `Gi0/0.40` | 40 | `10.10.40.100` | `255.255.255.0` |
| `Gi0/0` | `Gi0/0.50` | 50 | `10.10.50.100` | `255.255.255.0` |
| `Gi0/0` | `Gi0/0.60` | 60 | `10.10.60.100` | `255.255.255.0` |

### 🔗 Router-on-a-Stick — Explanation

**Router-on-a-Stick** is a network architecture in which one physical router interface uses multiple logical subinterfaces to route traffic between VLANs.

In this project, `GigabitEthernet0/0` connects the DHCP router to the switching infrastructure. Each subinterface is associated with a VLAN using IEEE 802.1Q encapsulation.

#### 🔄 How It Works

1. **Physical Interface:** `GigabitEthernet0/0` connects the router to the switch.
2. **Subinterfaces:** Separate logical interfaces are created for VLANs 10, 20, 30, 40, 50, and 60.
3. **VLAN Tagging:** IEEE 802.1Q identifies the VLAN associated with each tagged Ethernet frame.
4. **Gateway Addresses:** Each subinterface receives an IP address from its corresponding VLAN subnet.
5. **Inter-VLAN Routing:** The router routes traffic between VLANs when routing and interface configurations are operational.

#### 📋 Router-on-a-Stick Details

| Parameter | Description |
|---|---|
| Router Name | `DHCP` |
| Physical Interface | `GigabitEthernet0/0` |
| Architecture | Router-on-a-Stick |
| Encapsulation | IEEE 802.1Q |
| Subinterfaces | 6 |
| Supported VLANs | 10, 20, 30, 40, 50, 60 |
| Purpose | VLAN gateway connectivity and inter-VLAN routing |


## 📍 2.2 DHCP Router — DHCP Pool Configuration

### 🎯 Objective

Configure six DHCP pools on the `DHCP` router to dynamically assign IPv4 addresses to endpoints in their respective VLANs.

### ⚙️ Tasks to Perform

1. Create a DHCP pool for each departmental VLAN.
2. Define the correct network address and subnet mask for each pool.
3. Configure the appropriate default gateway using `default-router`.
4. Verify DHCP pool configuration.
5. Confirm that clients receive valid IP addresses.
6. Test connectivity between clients.

### 🌐 DHCP Pool Addressing Plan

| DHCP Pool | VLAN | Network Address | Default Gateway |
|---|---:|---|---|
| `VLAN10` | 10 | `10.10.10.0/24` | `10.10.10.1` |
| `VLAN20` | 20 | `10.10.20.0/24` | `10.10.20.1` |
| `VLAN30` | 30 | `10.10.30.0/24` | `10.10.30.1` |
| `VLAN40` | 40 | `10.10.40.0/24` | `10.10.40.1` |
| `VLAN50` | 50 | `10.10.50.0/24` | `10.10.50.1` |
| `VLAN60` | 60 | `10.10.60.0/24` | `10.10.60.1` |

### 📡 DHCP Configuration — Explanation

The DHCP router maintains separate address pools for the six departmental subnets. Each pool specifies the network from which addresses are allocated and the default gateway clients should use.

When a client requests an address, the DHCP server selects an available address from the appropriate subnet and provides the corresponding network configuration.

**Important:** The subinterface addresses ending in `.100` are configured on the DHCP router, while the DHCP pools advertise gateway addresses ending in `.1`. These are different addresses serving different purposes.

<br>


### 📸 Interface and Subinterface Configuration
<br>
<img width="1782" height="752" alt="Screenshot 2026-10-09 215647" src="https://github.com/user-attachments/assets/9803544b-5d17-42c3-b3ed-10df59ad74f2" />

<br>


### 📸 DHCP Pool Configuration
<br>
<img width="1797" height="956" alt="Screenshot 2026-10-09 215655" src="https://github.com/user-attachments/assets/aeb28eb6-df3b-45b5-a8ff-e199b8f4c9d7" />
<br>

---

## 📍 2.3 DHCP Client Address Allocation and Connectivity Testing

### 🎯 Objective

Verify that endpoints across the departmental VLANs receive their IP addresses dynamically from the centralized DHCP router and can communicate according to the network's routing and security configuration.

### ⚙️ Tasks to Perform

1. Request an IP address on each client using the DHCP command.
2. Verify the assigned IP address, subnet mask, and default gateway.
3. Test connectivity between clients within the same VLAN.
4. Test connectivity between clients in different VLANs.
5. Investigate any failures using gateway and routing diagnostics.


### 📸 DHCP Allocation and Same-VLAN Connectivity

The screenshots demonstrate successful DHCP allocation for the two VLAN 10 clients and successful ICMP communication between them.
<br>
<img width="1728" height="616" alt="Screenshot 2026-10-09 232218" src="https://github.com/user-attachments/assets/61073822-bb36-408a-aa48-31c7d5496313" />
<br>


### 📸 Cross-VLAN Connectivity Testing

The screenshots also demonstrate connectivity testing between the VLAN 40 and VLAN 50 endpoints.

<br>
<img width="1796" height="792" alt="Screenshot 2026-10-10 003715" src="https://github.com/user-attachments/assets/2a731d0b-43e5-49fd-a677-53dae21af291" />

<br>

### 🎯 Phase 2 Result

The DHCP router is configured with IEEE 802.1Q subinterfaces for the six departmental VLANs and DHCP pools for the corresponding IPv4 subnets. DHCP allocation and same-VLAN connectivity are demonstrated by the supplied screenshots. Cross-VLAN connectivity requires further verification because some ping attempts are being administratively prohibited.

---





# 🔹 Phase 3 — Access Control Lists (ACLs) and Inter-VLAN Traffic Security

## 🎯 Objective

Implement Extended Access Control Lists (ACLs) on `MAIN_SWITCH` to enforce departmental network segmentation and controlled inter-VLAN communication. Configure `VLAN-POLICY` to allow `PC6_VLAN60_MANAGER` to communicate with endpoints across all VLANs, while restricting endpoints in VLANs 10, 20, 30, 40, and 50 to communicate only within their respective VLANs. Verify ACL functionality using ICMP connectivity tests between same-VLAN and different-VLAN endpoints.

---

## 📍 3.1 MAIN_SWITCH — Extended ACL Configuration

### 🎯 Objective

Create an extended ACL named `VLAN-POLICY` to control traffic between departmental VLANs according to the network security policy.

### ⚙️ Tasks to Perform

1. Create an extended ACL named `VLAN-POLICY`.
2. Permit VLAN 60 traffic to communicate with all IP networks.
3. Permit same-VLAN communication for VLANs 10, 20, 30, 40, 50, and 60.
4. Deny inter-VLAN communication for VLANs 10, 20, 30, 40, and 50.
5. Apply the ACL inbound on the relevant VLAN interfaces.
6. Save the configuration.
7. Verify ACL entries and interface assignments.
8. Test connectivity between endpoints across different VLANs.

### 🛡️ Network Access Policy

| Source VLAN | Same-VLAN Communication | Communication with Other VLANs |
|---|---|---|
| VLAN 10 — IT | ✅ Allowed | ❌ Denied |
| VLAN 20 — HR | ✅ Allowed | ❌ Denied |
| VLAN 30 — MGMT | ✅ Allowed | ❌ Denied |
| VLAN 40 — CYBER | ✅ Allowed | ❌ Denied |
| VLAN 50 — FINANCE | ✅ Allowed | ❌ Denied |
| VLAN 60 — MANAGER | ✅ Allowed | ✅ Allowed |

### 🔐 ACL Configuration Details

| Parameter | Description |
|---|---|
| Device | `MAIN_SWITCH` |
| ACL Name | `VLAN-POLICY` |
| ACL Type | Extended Named ACL |
| Policy Enforcement | Inbound on VLAN interfaces |
| Manager VLAN | VLAN 60 |
| Manager IP Subnet | `10.10.60.0/24` |
| Purpose | Enforce departmental isolation and manager access |

### 📸 Extended ACL Configuration
<img width="1271" height="352" alt="Screenshot 2026-10-12 113246" src="https://github.com/user-attachments/assets/29717f9c-6f3f-4282-a656-f07904ce7390" />

---

## 📍 3.2 MAIN_SWITCH — ACL Application to VLAN Interfaces

### 🎯 Objective

Apply the `VLAN-POLICY` ACL inbound on the departmental VLAN interfaces to enforce the configured traffic restrictions.

### ⚙️ Tasks to Perform

1. Apply `VLAN-POLICY` inbound on `Vlan10`.
2. Apply `VLAN-POLICY` inbound on `Vlan20`.
3. Apply `VLAN-POLICY` inbound on `Vlan30`.
4. Apply `VLAN-POLICY` inbound on `Vlan40`.
5. Apply `VLAN-POLICY` inbound on `Vlan50`.
6. Apply `VLAN-POLICY` inbound on `Vlan60`.
7. Save the configuration.
8. Verify ACL assignments on the VLAN interfaces.

### 🔌 ACL Interface Assignment Plan

| VLAN Interface | VLAN Name | ACL Name | Direction |
|---|---|---|---|
| `Vlan10` | IT | `VLAN-POLICY` | Inbound |
| `Vlan20` | HR | `VLAN-POLICY` | Inbound |
| `Vlan30` | MGMT | `VLAN-POLICY` | Inbound |
| `Vlan40` | CYBER | `VLAN-POLICY` | Inbound |
| `Vlan50` | FINANCE | `VLAN-POLICY` | Inbound |
| `Vlan60` | MANAGER | `VLAN-POLICY` | Inbound |

### 📸 ACL Application to VLAN Interfaces

<img width="1722" height="691" alt="Screenshot 2026-10-10 003747" src="https://github.com/user-attachments/assets/7eb6ce19-156b-4752-b478-8ee0241a3e73" />



---

## 📍 3.3 ACL Verification — Same-VLAN and Inter-VLAN Connectivity

### 🎯 Objective

Verify that the ACL policy permits communication between endpoints in the same VLAN, blocks unauthorized communication between departmental VLANs, and allows the Manager VLAN to communicate with endpoints across all VLANs.

### ⚙️ Tasks to Perform

1. Test connectivity between two VLAN 10 endpoints.
2. Test connectivity between VLAN 10 and VLAN 60.
3. Test connectivity between VLAN 20 and VLAN 60.
4. Test connectivity between VLAN 40 and VLAN 60.
5. Test connectivity between VLAN 50 and VLAN 60.
6. Verify that non-manager VLANs cannot initiate communication with other departmental VLANs.
7. Confirm that ACL counters reflect the expected traffic.

### 📸 Same-VLAN Connectivity Testing

**Test:** Verify communication between endpoints in the same VLAN.

<br>
<img width="1728" height="616" alt="Screenshot 2026-10-09 232218" src="https://github.com/user-attachments/assets/61073822-bb36-408a-aa48-31c7d5496313" />
<br>

### 📸 Manager VLAN Connectivity Testing

**Test:** Verify that `PC6_VLAN60_MANAGER` can communicate with endpoints in other VLANs.

<img width="1722" height="662" alt="Screenshot 2026-10-10 015559" src="https://github.com/user-attachments/assets/7b08acc9-f892-49d9-b29f-96b9020f8105" />


### 📸 Restricted Inter-VLAN Connectivity Testing


<img width="1775" height="757" alt="Screenshot 2026-10-10 015724" src="https://github.com/user-attachments/assets/f97b3d69-bac8-43db-a3ce-7924b79c2eba" />


---




# 🔹 Phase 4 — Disable DTP Negotiation Using `switchport nonegotiate`

## 🎯 Objective

Secure the campus switching infrastructure by disabling Dynamic Trunking Protocol (DTP) negotiation on switch interfaces connected to other switches. This configuration reduces the risk of unauthorized trunk formation by preventing interfaces from negotiating trunk links through DTP. Apply `switchport nonegotiate` to the relevant switch-to-switch interfaces on `SW_FLR1`, `SW_GROUND_FLR`, and `MAIN_SWITCH`, while maintaining the existing access and trunk configurations.

---

## 🏢 Real-World Scenario — Campus Secure Network

Consider the campus network of a small company with two floors, multiple departments, and a central switch connecting the entire infrastructure.

The network contains six departmental VLANs:

- **VLAN 10 — IT**
- **VLAN 20 — HR**
- **VLAN 30 — MGMT**
- **VLAN 40 — CYBER**
- **VLAN 50 — FINANCE**
- **VLAN 60 — MANAGER**

The floor switches connect departmental endpoints to the network, while `MAIN_SWITCH` provides connectivity between the floors.


<br>


```mermaid
flowchart TB
    DHCP["DHCP_SERVER<br/>10.10.10.100"]
    MAIN["MAIN_SWITCH<br/>Central Layer 3 Switch<br/>VLAN Gateways | ACL Policies"]
    ATTACKER["⚠️ ATTACKER<br/>Unauthorized Switch<br/>DTP Negotiation Attempt"]

    DHCP --- MAIN

    subgraph FLOOR1["🏢 FIRST FLOOR"]
        direction TB
        SW1["SW_FLR1<br/>DTP Disabled on Appropriate Ports"]
        IT1["IT PC<br/>VLAN 10<br/>10.10.10.2"]
        HR["HR PC<br/>VLAN 20<br/>10.10.20.2"]
        MGMT["Management PC<br/>VLAN 30<br/>10.10.30.2"]
        CYBER1["Cybersecurity PC<br/>VLAN 40<br/>10.10.40.3"]

        SW1 --- IT1
        SW1 --- HR
        SW1 --- MGMT
        SW1 --- CYBER1
    end

    subgraph GROUND["🏢 GROUND FLOOR"]
        direction TB
        SW2["SW_GROUND_FLR<br/>DTP Disabled on Appropriate Ports"]
        IT2["IT PC<br/>VLAN 10<br/>10.10.10.3"]
        CYBER2["Cybersecurity PC<br/>VLAN 40<br/>10.10.40.4"]
        FINANCE["Finance PC<br/>VLAN 50<br/>10.10.50.2"]
        MANAGER["Manager PC<br/>VLAN 60<br/>10.10.60.3"]

        SW2 --- IT2
        SW2 --- CYBER2
        SW2 --- FINANCE
        SW2 --- MANAGER
    end

    MAIN --- SW1
    MAIN --- SW2
    SW1 ---|"Inter-switch Link"| SW2

    ATTACKER -.->|"DTP Attack Attempt"| SW1

    classDef core fill:#cfe2f3,stroke:#1155cc,stroke-width:2px,color:#000;
    classDef switch fill:#d9ead3,stroke:#38761d,stroke-width:2px,color:#000;
    classDef endpoint fill:#f3f3f3,stroke:#666,stroke-width:1px,color:#000;
    classDef attacker fill:#f4cccc,stroke:#cc0000,stroke-width:2px,color:#990000;

    class MAIN,DHCP core;
    class SW1,SW2 switch;
    class IT1,HR,MGMT,CYBER1,IT2,CYBER2,FINANCE,MANAGER endpoint;
    class ATTACKER attacker;
```



<br>


An attacker gains access to an unused switch port and connects a rogue switch configured to negotiate a trunk link. If the legitimate switch port is configured to negotiate trunking, the attacker may attempt to establish an unauthorized trunk and gain access to traffic from multiple VLANs.

To reduce this risk, the company disables DTP negotiation on the relevant switch interfaces.

---

## ⚠️ Security Problem — Unauthorized Trunk Negotiation

### 🚨 What Can an Attacker Attempt?

1. Connect a rogue switch to an accessible switch port.
2. Configure the rogue switch to send DTP negotiation messages.
3. Attempt to establish a trunk link with the legitimate switch.
4. If trunk negotiation succeeds and the allowed VLANs are accessible, potentially gain access to traffic from multiple VLANs.

> **🔐 Security Risk:** Unauthorized trunk formation can undermine VLAN segmentation and expose traffic that should remain separated. Successful exploitation depends on the switch configuration, platform behavior, and other security controls.

---

## 🛡️ Solution — Disable DTP Negotiation

### 🎯 Configuration Strategy

Use the Cisco IOS command `switchport nonegotiate` on the relevant switch-to-switch interfaces.

This disables DTP negotiation on those interfaces. It does **not**, by itself, disable trunking: a statically configured trunk can continue to operate without DTP negotiation.

### ⚙️ Tasks to Perform

1. Identify the switch-to-switch interfaces in the topology.
2. Apply `switchport nonegotiate` to the relevant interfaces on `SW_FLR1`.
3. Apply `switchport nonegotiate` to the relevant interfaces on `SW_GROUND_FLR`.
4. Apply `switchport nonegotiate` to the relevant interfaces on `MAIN_SWITCH`.
5. Preserve the existing access VLAN assignments on endpoint-facing ports.
6. Preserve the static trunk configuration on trunk links.
7. Verify that trunk links remain operational and DTP negotiation is disabled.
8. Save the configurations.

---

## 📍 4.1 SW_FLR1 — Disable DTP Negotiation

### 🎯 Objective

Disable DTP negotiation on the relevant interfaces of `SW_FLR1` to prevent dynamic trunk negotiation.

### ⚙️ Configuration

<img width="1537" height="757" alt="Screenshot 2026-10-09 205943" src="https://github.com/user-attachments/assets/d3ec5018-50c8-45c2-bc0f-df195eaf8dab" />


### 🔍 Verification

```cisco
show interfaces switchport

```
<img width="1512" height="517" alt="Screenshot 2026-10-09 210125" src="https://github.com/user-attachments/assets/2420c47d-5610-401a-9f0b-625ce7593005" />

---

## 📍 4.2 SW_GROUND_FLR — Disable DTP Negotiation

### 🎯 Objective

Disable DTP negotiation on the relevant interfaces of `SW_GROUND_FLR` while maintaining the existing access-port and trunk configurations.

### ⚙️ Configuration

<img width="1571" height="807" alt="Screenshot 2026-10-09 212253" src="https://github.com/user-attachments/assets/da2c4559-4dc5-40ba-bf9f-6d4589484b8a" />

### 🔍 Verification

```cisco
show interfaces switchport
```
<img width="1000" height="467" alt="Screenshot 2026-10-09 212420" src="https://github.com/user-attachments/assets/e433a617-5087-47dd-9bda-b3046cd65d0b" />

---

## 📍 4.3 MAIN_SWITCH — Disable DTP Negotiation

### 🎯 Objective

Disable DTP negotiation on the relevant interfaces of `MAIN_SWITCH` to maintain controlled trunk operation between the central switch and the floor switches.

### ⚙️ Configuration


<br>
<img width="1402" height="367" alt="Screenshot 2026-10-09 214357" src="https://github.com/user-attachments/assets/21d4bbcc-5212-4130-9e5c-ec75aeeb9dc9" />

<br>


### 🔍 Verification



---

### 🔐 ATTACK PREVENTION

* 🛡️ DTP Exploitation
* 🛡️ VLAN Hopping
* 🛡️ MAC Flooding
* 🛡️ Rogue DHCP Server
* 🛡️ ARP Spoofing
* 🛡️ STP Manipulation
* 🛡️ Unauthorized Device Connection



### 🎯 Phase 4 Result

The campus switching infrastructure is configured to disable DTP negotiation on the selected interfaces while preserving the intended access and trunk configurations. This adds a Layer 2 security measure to the Campus Secure Network project.

---




# 🔹 Phase 5 — DHCP Snooping, Dynamic ARP Inspection (DAI) and DHCP Rate Limiting

## 🎯 Objective

Implement Layer 2 security across `SW_FLR1`, `SW_GROUND_FLR`, and `MAIN_SWITCH` by enabling DHCP Snooping and Dynamic ARP Inspection (DAI) for VLANs 10, 20, 30, 40, 50, and 60. Configure trusted interfaces on legitimate switch uplinks and the DHCP server connection, and apply DHCP rate limits on selected interfaces of `MAIN_SWITCH` to help protect the campus network against rogue DHCP servers, ARP spoofing, and DHCP starvation attempts. Finally, verify the security configurations using Cisco IOS commands and validate that legitimate DHCP allocation and endpoint connectivity continue to work.

---

## 🏢 5.0 Real-World Scenario — Campus Secure Network

A company operates a two-floor office network with six departmental VLANs. Employees connect through `SW_FLR1` and `SW_GROUND_FLR`, while `MAIN_SWITCH` provides central switching, VLAN gateway connectivity, and access to the centralized DHCP server.

An attacker connects an unauthorized device to an accessible switch port and attempts to disrupt network services by introducing a rogue DHCP server, sending forged ARP messages, or generating excessive DHCP requests. These activities can cause incorrect IP configuration, traffic interception, DHCP address exhaustion, or network connectivity problems.

To mitigate these threats, the company implements **DHCP Snooping, Dynamic ARP Inspection (DAI), and DHCP Rate Limiting** across the switching infrastructure. Legitimate infrastructure-facing interfaces are trusted where required, while endpoint-facing interfaces remain untrusted.

---

## 🌐 5.0.1 Security Topology

```mermaid
flowchart TB
    DHCP["DHCP_SERVER<br/>10.10.10.100"]
    MAIN["MAIN_SWITCH<br/>DHCP Snooping | DAI | Rate Limiting"]
    ATTACKER["⚠️ ATTACKER<br/>Rogue DHCP Server<br/>ARP Spoofing"]

    DHCP ---|"Trusted DHCP Connection"| MAIN

    subgraph FLOOR1["🏢 FIRST FLOOR"]
        direction TB
        SW1["SW_FLR1<br/>DHCP Snooping + DAI"]
        IT1["IT PC<br/>VLAN 10<br/>10.10.10.2"]
        HR["HR PC<br/>VLAN 20<br/>10.10.20.2"]
        MGMT["Management PC<br/>VLAN 30<br/>10.10.30.2"]
        CYBER1["Cybersecurity PC<br/>VLAN 40<br/>10.10.40.3"]

        SW1 --- IT1
        SW1 --- HR
        SW1 --- MGMT
        SW1 --- CYBER1
    end

    subgraph GROUND["🏢 GROUND FLOOR"]
        direction TB
        SW2["SW_GROUND_FLR<br/>DHCP Snooping + DAI"]
        IT2["IT PC<br/>VLAN 10<br/>10.10.10.3"]
        CYBER2["Cybersecurity PC<br/>VLAN 40<br/>10.10.40.4"]
        FINANCE["Finance PC<br/>VLAN 50<br/>10.10.50.2"]
        MANAGER["Manager PC<br/>VLAN 60<br/>10.10.60.3"]

        SW2 --- IT2
        SW2 --- CYBER2
        SW2 --- FINANCE
        SW2 --- MANAGER
    end

    MAIN ---|"Trusted Uplink"| SW1
    MAIN ---|"Trusted Uplink"| SW2
    SW1 ---|"Inter-switch Link"| SW2

    ATTACKER -.->|"Rogue DHCP / Forged ARP"| SW1

    classDef core fill:#cfe2f3,stroke:#1155cc,stroke-width:2px,color:#000;
    classDef switch fill:#d9ead3,stroke:#38761d,stroke-width:2px,color:#000;
    classDef endpoint fill:#f3f3f3,stroke:#666,stroke-width:1px,color:#000;
    classDef attacker fill:#f4cccc,stroke:#cc0000,stroke-width:2px,color:#990000;

    class MAIN,DHCP core;
    class SW1,SW2 switch;
    class IT1,HR,MGMT,CYBER1,IT2,CYBER2,FINANCE,MANAGER endpoint;
    class ATTACKER attacker;
```

---

## 🚨 5.0.2 Potential Attacks and Prevention

| No. | Attack Name | Potential Impact | Security Mechanism |
|---:|---|---|---|
| 1 | **Rogue DHCP Server Attack** | An unauthorized DHCP server distributes incorrect IP addresses, gateways, or DNS settings. | DHCP Snooping |
| 2 | **DHCP Starvation Attack** | Excessive DHCP requests attempt to exhaust the available DHCP address pool. | DHCP rate limiting and additional DHCP protections |
| 3 | **ARP Spoofing / ARP Poisoning** | Forged ARP messages attempt to associate an attacker's MAC address with another device's IP address. | Dynamic ARP Inspection (DAI) |
| 4 | **Man-in-the-Middle (MITM) Attack** | An attacker attempts to intercept or manipulate traffic between communicating devices. | DAI and additional network security controls |
| 5 | **Unauthorized DHCP Responses** | A rogue device attempts to send unauthorized DHCP Offer or ACK messages to clients. | DHCP Snooping |
| 6 | **Excessive DHCP Traffic** | A device generates DHCP traffic at an abnormal rate, potentially disrupting network services. | DHCP rate limiting |

> **Security note:** DHCP Snooping and DAI mitigate specific Layer 2 threats but do not eliminate every attack. DHCP rate limiting restricts excessive DHCP packet rates; it does not independently prevent all DHCP starvation attacks.

---

## 📍 5.1 SW_FLR1 — DHCP Snooping and Dynamic ARP Inspection

### 🎯 Objective

Enable DHCP Snooping and Dynamic ARP Inspection on the first-floor switch for all six departmental VLANs. Configure legitimate infrastructure-facing interfaces as trusted where appropriate, while keeping endpoint-facing interfaces untrusted.

### ⚙️ Tasks to Perform

1. Enable DHCP Snooping globally.
2. Enable DHCP Snooping for VLANs 10, 20, 30, 40, 50, and 60.
3. Enable Dynamic ARP Inspection for the same VLANs.
4. Trust the legitimate uplinks toward `MAIN_SWITCH` and `SW_GROUND_FLR`, according to the actual topology.
5. Keep endpoint-facing ports untrusted.
6. Verify DHCP Snooping operation and interface trust settings.
7. Verify Dynamic ARP Inspection interface states.
8. Save the configuration.

### 🔐 Security Configuration

<br>
<img width="1712" height="517" alt="Screenshot 2026-10-09 210826" src="https://github.com/user-attachments/assets/73cc9ff0-e62e-4dcf-b476-21dc3273d328" />

<br>





### 🔍 Verification

```cisco
show ip dhcp snooping
show ip arp inspection interfaces
```

<br>

<img width="1043" height="296" alt="Screenshot 2026-10-09 210923" src="https://github.com/user-attachments/assets/66633450-1dca-4612-915c-51b58eb85a2a" />


<br>
---

## 📍 5.2 SW_GROUND_FLR — DHCP Snooping and Dynamic ARP Inspection

### 🎯 Objective

Enable DHCP Snooping and Dynamic ARP Inspection on the ground-floor switch to protect departmental VLANs against unauthorized DHCP responses and ARP spoofing.

### ⚙️ Tasks to Perform

1. Enable DHCP Snooping globally.
2. Enable DHCP Snooping for VLANs 10, 20, 30, 40, 50, and 60.
3. Enable Dynamic ARP Inspection for the same VLANs.
4. Trust legitimate infrastructure-facing uplinks according to the actual topology.
5. Keep endpoint-facing ports untrusted.
6. Verify DHCP Snooping and DAI operation.
7. Save the configuration.

### 🔐 Security Configuration
<img width="1512" height="600" alt="Screenshot 2026-10-09 215224" src="https://github.com/user-attachments/assets/11d34ca8-698e-4d18-b529-428d23407f43" />



### 🔍 Verification

```cisco
show ip dhcp snooping
show ip arp inspection interfaces
```

---

## 📍 5.3 MAIN_SWITCH — DHCP Snooping and Dynamic ARP Inspection

### 🎯 Objective

Enable DHCP Snooping and Dynamic ARP Inspection on `MAIN_SWITCH`, trust the legitimate DHCP server connection and infrastructure-facing uplinks, and configure DHCP rate limits on selected interfaces.

### ⚙️ Tasks to Perform

1. Enable DHCP Snooping globally.
2. Enable DHCP Snooping for VLANs 10, 20, 30, 40, 50, and 60.
3. Enable Dynamic ARP Inspection for the same VLANs.
4. Trust the interface connected to the legitimate DHCP server, if that interface carries the relevant DHCP server messages.
5. Trust legitimate switch uplinks where required.
6. Keep untrusted endpoint-facing interfaces protected.
7. Configure DHCP rate limits on suitable untrusted interfaces.
8. Verify DHCP Snooping, DAI, and rate-limit configuration.
9. Save the configuration.

### 🔐 DHCP Snooping and DAI Configuration

<img width="1320" height="702" alt="Screenshot 2026-10-09 214425" src="https://github.com/user-attachments/assets/1745ec03-e5c8-49f1-a0bf-59722ae37b8d" />

### 🚦 DHCP Rate Limiting

The following table reflects the rate-limit values shown in your configuration screenshots.

| Interface | Intended Connection | DHCP Rate Limit |
|---|---|---:|
| `GigabitEthernet0/0` | DHCP server connection | 10 packets per second |
| `GigabitEthernet0/1` | Selected interface | 5 packets per second |
| `GigabitEthernet1/1` | Selected interface | 5 packets per second |

### ⚙️ Rate-Limit Configuration
<img width="1258" height="532" alt="Screenshot 2026-10-09 214411" src="https://github.com/user-attachments/assets/b6f9ebed-104a-4245-9c6f-d6a4737f90b6" />

### 🔍 Verification

```cisco
show ip dhcp snooping
show ip dhcp snooping binding
show ip arp inspection interfaces
show ip arp inspection vlan
show running-config
```

---

### 🎯 Phase 5 Result

DHCP Snooping, Dynamic ARP Inspection, trusted-interface configuration, and DHCP rate limiting are implemented across the campus switching infrastructure. These controls help protect the departmental VLANs against rogue DHCP servers, ARP spoofing, and excessive DHCP traffic while preserving legitimate network services.

---





# 🔹 Phase 6 — Spanning Tree Protocol (STP) Security: PortFast, BPDU Guard and Root Guard

## 🎯 Objective

Implement Layer 2 security across `SW_FLR1`, `SW_GROUND_FLR`, and `MAIN_SWITCH` by configuring PortFast and BPDU Guard on endpoint-facing access ports and Root Guard on selected downstream-facing interfaces of the main switch. These configurations help protect the campus network against unauthorized switches, rogue STP root bridge elections, and potential Layer 2 switching loops.

---

## 🏢 6.0 Real-World Scenario — Campus Network Security

In a company office, employees connect their computers to access switches on the first and ground floors. `MAIN_SWITCH` provides central connectivity and acts as the intended STP root bridge.

An attacker could connect an unauthorized switch to an employee-facing port or attempt to influence STP root bridge elections by sending malicious Bridge Protocol Data Units (BPDUs).

These actions may cause unexpected changes to the STP topology, traffic disruption, or Layer 2 switching loops.

To reduce these risks, the company implements **PortFast, BPDU Guard, and Root Guard** on appropriate interfaces.

---

## 🌐 6.0.1 STP Security Topology

```mermaid
flowchart TB
    MAIN["MAIN_SWITCH<br/>Intended STP Root Bridge<br/>Root Guard on Selected Ports"]
    ATTACKER["⚠️ ATTACKER<br/>Unauthorized Switch<br/>Malicious BPDU"]

    subgraph FLOOR1["🏢 FIRST FLOOR"]
        direction TB
        SW1["SW_FLR1<br/>PortFast + BPDU Guard"]
        IT1["IT PC<br/>VLAN 10"]
        HR["HR PC<br/>VLAN 20"]
        MGMT["Management PC<br/>VLAN 30"]
        CYBER1["Cybersecurity PC<br/>VLAN 40"]

        SW1 --- IT1
        SW1 --- HR
        SW1 --- MGMT
        SW1 --- CYBER1
    end

    subgraph GROUND["🏢 GROUND FLOOR"]
        direction TB
        SW2["SW_GROUND_FLR<br/>PortFast + BPDU Guard"]
        IT2["IT PC<br/>VLAN 10"]
        CYBER2["Cybersecurity PC<br/>VLAN 40"]
        FINANCE["Finance PC<br/>VLAN 50"]
        MANAGER["Manager PC<br/>VLAN 60"]

        SW2 --- IT2
        SW2 --- CYBER2
        SW2 --- FINANCE
        SW2 --- MANAGER
    end

    MAIN ---|"Root Guard on Selected Ports"| SW1
    MAIN ---|"Root Guard on Selected Ports"| SW2
    SW1 ---|"Inter-switch Link"| SW2

    ATTACKER -.->|"Malicious BPDU Attempt"| SW1

    classDef core fill:#cfe2f3,stroke:#1155cc,stroke-width:2px,color:#000;
    classDef switch fill:#d9ead3,stroke:#38761d,stroke-width:2px,color:#000;
    classDef endpoint fill:#f3f3f3,stroke:#666,stroke-width:1px,color:#000;
    classDef attacker fill:#f4cccc,stroke:#cc0000,stroke-width:2px,color:#990000;

    class MAIN core;
    class SW1,SW2 switch;
    class IT1,HR,MGMT,CYBER1,IT2,CYBER2,FINANCE,MANAGER endpoint;
    class ATTACKER attacker;
```

---

## 🚨 6.0.2 Potential STP Security Threats

| No. | Attack / Threat | Potential Impact | Prevention Mechanism |
|---:|---|---|---|
| 1 | Rogue Switch Connection | An unauthorized switch may introduce unwanted Layer 2 connectivity. | BPDU Guard on endpoint-facing ports |
| 2 | Rogue Root Bridge Attack | An attacker attempts to become the STP root bridge by sending superior BPDUs. | Root Guard on selected downstream-facing ports |
| 3 | STP Topology Manipulation | Unauthorized BPDUs may influence spanning-tree decisions. | BPDU Guard and Root Guard, according to port role |
| 4 | Layer 2 Switching Loop | Incorrect switch connections may create forwarding loops. | STP and correct switch configuration |
| 5 | Broadcast Storm | A Layer 2 loop may cause excessive broadcast traffic. | STP loop prevention and appropriate Layer 2 security controls |

---

## 📍 6.1 SW_FLR1 — PortFast and BPDU Guard Configuration

### 🎯 Objective

Configure PortFast and BPDU Guard on endpoint-facing access ports of `SW_FLR1` to allow connected endpoints to enter the forwarding state quickly and protect against unauthorized switches sending BPDUs.

### ⚙️ Tasks to Perform

1. Identify endpoint-facing access interfaces.
2. Enable PortFast on the appropriate access ports.
3. Enable BPDU Guard on the same endpoint-facing ports.
4. Keep trunk interfaces excluded from this access-port configuration.
5. Verify PortFast and BPDU Guard operation.
6. Save the configuration.

### 🔐 Security Configuration



| Interface | Connected Endpoint | Configuration |
|---|---|---|
| `Gi0/1` | IT PC — VLAN 10 | PortFast + BPDU Guard |
| `Gi0/2` | HR PC — VLAN 20 | PortFast + BPDU Guard |
| `Gi0/3` | Management PC — VLAN 30 | PortFast + BPDU Guard |
| `Gi1/0` | Cybersecurity PC — VLAN 40 | PortFast + BPDU Guard |
| `Gi0/0` | Trunk uplink | Excluded |
| `Gi1/1` | Trunk uplink | Excluded |



### 📸 Configuration Evidence
<br>
<img width="1426" height="805" alt="Screenshot 2026-10-09 212527" src="https://github.com/user-attachments/assets/b2a342d7-60b4-452e-99ba-e020ac448946" />

<br>

---

## 📍 6.2 SW_GROUND_FLR — PortFast and BPDU Guard Configuration

### 🎯 Objective

Configure PortFast and BPDU Guard on endpoint-facing access ports of `SW_GROUND_FLR` to prevent unauthorized switches from influencing spanning-tree operation through employee-facing connections.

### ⚙️ Tasks to Perform

1. Identify endpoint-facing access interfaces.
2. Enable PortFast on the appropriate access ports.
3. Enable BPDU Guard on the same endpoint-facing ports.
4. Exclude trunk interfaces from the access-port configuration.
5. Verify PortFast and BPDU Guard operation.
6. Save the configuration.

### 🔐 Security Configuration


| Interface | Connected Endpoint | Configuration |
|---|---|---|
| `Gi0/1` | Cybersecurity PC — VLAN 40 | PortFast + BPDU Guard |
| `Gi0/2` | Finance PC — VLAN 50 | PortFast + BPDU Guard |
| `Gi0/3` | Manager PC — VLAN 60 | PortFast + BPDU Guard |
| `Gi1/0` | Additional endpoint, if connected | PortFast + BPDU Guard |
| `Gi0/0` | Trunk uplink, if configured | Excluded |
| `Gi1/1` | Trunk uplink | Excluded |





### 📸 Configuration Evidence

<img width="1662" height="768" alt="Screenshot 2026-10-09 213414" src="https://github.com/user-attachments/assets/cd75ae09-72a2-44eb-9ab5-6104835764cb" />


---

## 📍 6.3 MAIN_SWITCH — Root Guard Configuration

### 🎯 Objective

Configure Root Guard on selected downstream-facing interfaces of `MAIN_SWITCH` to prevent connected downstream switches from becoming the STP root bridge through superior BPDU advertisements.

### ⚙️ Tasks to Perform

1. Confirm that `MAIN_SWITCH` is intended to be the STP root bridge.
2. Identify the downstream-facing trunk interfaces connected to `SW_FLR1` and `SW_GROUND_FLR`.
3. Enable Root Guard on the selected interfaces.
4. Verify the spanning-tree root bridge and interface configuration.
5. Save the configuration.

### 🔐 Security Configuration

| Interface | Connected Device | Configuration |
|---|---|---|
| `Gi0/1` | `SW_FLR1` | Root Guard |
| `Gi1/1` | `SW_GROUND_FLR` | Root Guard |



### 📸 Configuration Evidence

<img width="1367" height="487" alt="Screenshot 2026-10-09 214418" src="https://github.com/user-attachments/assets/7521fa57-afaa-4b74-821c-34516adfcba8" />


---




## 📋 Phase 6 — Implementation Summary

| Task | Device | Security Configuration |
|---|---|---|
| 6.1 | `SW_FLR1` | PortFast + BPDU Guard |
| 6.2 | `SW_GROUND_FLR` | PortFast + BPDU Guard |
| 6.3 | `MAIN_SWITCH` | Root Guard |
| 6.4 | All switches | STP security verification |
| 6.5 | EVE-NG lab | Controlled security testing |

---

## ✅ Phase 6 — Final Result

PortFast and BPDU Guard are configured on selected endpoint-facing access ports of `SW_FLR1` and `SW_GROUND_FLR`. Root Guard is configured on the selected downstream-facing interfaces of `MAIN_SWITCH`.

These controls help protect the campus network against unauthorized STP participation, rogue root bridge attempts, and certain Layer 2 topology disruptions while preserving normal network operation.

---









# 🔄 7. Project Implementation Roadmap

| Phase | Title | Main Tasks |
|---|---|---|
| Phase 1 | VLANs and Interface Configuration | VLAN creation, access ports, trunk links, VLAN interfaces, and DHCP relay planning |
| Phase 2 | Centralized DHCP Using Router-on-a-Stick | 802.1Q subinterfaces, DHCP pools, client address allocation, and connectivity testing |
| Phase 3 | ACLs and Inter-VLAN Traffic Security | Manager VLAN access policy, departmental isolation, ACL application, and connectivity verification |
| Phase 4 | DTP Security | Disable DTP negotiation on appropriate switch interfaces and verify static trunk operation |
| Phase 5 | DHCP Snooping, DAI, and DHCP Rate Limiting | Protect against rogue DHCP, ARP spoofing, and excessive DHCP traffic |
| Phase 6 | STP Security | PortFast, BPDU Guard, and Root Guard on appropriate interfaces |

---

# 🧪 8. Verification and Testing

| Test | Verification Command | Expected Result |
|---|---|---|
| VLAN configuration | `show vlan brief` | Correct VLAN IDs and names |
| Trunk operation | `show interfaces trunk` | Required VLANs permitted and active |
| Interface status | `show ip interface brief` | Configured interfaces operational |
| DHCP Snooping | `show ip dhcp snooping` | Snooping status and VLAN configuration visible |
| DHCP bindings | `show ip dhcp snooping binding` | Bindings visible for learned clients, where applicable |
| Gateway connectivity | `ping <gateway-IP>` | Successful response |
| DHCP allocation | Inspect endpoint IP configuration | Valid IP address, subnet mask, and gateway |
| Remote management | SSH client | Successful authenticated session |
| Packet analysis | Wireshark | Relevant protocol traffic captured |

---



<br>


https://github.com/user-attachments/assets/2c71e12b-5065-4c2d-b3e9-925cf49d7a7d


<br>


https://github.com/user-attachments/assets/bcc6d587-dd49-455c-9717-876c694baa22


<br>



https://github.com/user-attachments/assets/72e1e5c1-8f03-4ff2-817c-f1fc63f43957


<br>


https://github.com/user-attachments/assets/80831762-09c0-4ac4-a4ce-7f074c6f8fd3


<br>








---

# 🏆 9. Project Outcomes

This project demonstrates practical experience with:

- Enterprise VLAN segmentation.
- Access and trunk interface configuration.
- IPv4 addressing and subnetting.
- Centralized DHCP services.
- DHCP relay configuration.
- DHCP Snooping.
- Network troubleshooting.
- Secure SSH remote management.
- Wireshark packet analysis.
- Technical documentation and configuration verification.

---



## Author

**Project:** Campus Secure Network

**Platform:** EVE-NG

**Domain:** Enterprise Networking & Network Security

**Focus:** VLANs, Switching, DHCP, Routing, and Secure Management

---


