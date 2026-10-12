# 🔐 Campus Secure Network

### Enterprise VLAN Segmentation • Centralized DHCP • Secure Network Infrastructure

> **Enterprise Network Simulation | EVE-NG | IPv4 | VLANs | 802.1Q | DHCP | Network Security**

<div align="center">

**3 Switches** • **1 DHCP Server** • **8 Endpoints** • **6 VLANs** • **6 Departmental Networks**

</div>

---

## 🎯 1. Project Objective

Design and implement a simulated enterprise campus network representing a small company's office environment with multiple departments and floors.

The network uses VLAN-based segmentation to separate departmental traffic, access ports to connect end devices, and trunk links to transport multiple VLANs between switches.

A central switching device provides the VLAN gateway interfaces and DHCP relay functionality required by the design. A centralized DHCP server provides dynamic IP address allocation to the relevant client networks.

The project also incorporates DHCP Snooping, network connectivity verification, and secure network management concepts.

The implementation is performed in EVE-NG using Cisco IOS or compatible network device images.

### 🎯 Key Objectives

- 🏢 Design a realistic multi-floor company network.
- 🔀 Implement six departmental VLANs.
- 🔌 Configure access and trunk interfaces.
- 🌐 Configure VLAN interfaces and gateway addresses.
- 📡 Implement DHCP relay for centralized address allocation.
- 🛡️ Configure DHCP Snooping.
- 🔍 Verify VLAN membership and trunk operation.
- 🧪 Test endpoint connectivity and troubleshoot network issues.
- 🔐 Extend the infrastructure with SSH-only remote management.
- 📊 Document configurations and verification results.

---

# 🖥️ 2. Network Topology

## 🏢 Campus Network Architecture

The network is organized into three main switching components:

1. **MAIN_SWITCH** — Central switching device.
2. **SW_FLR1** — First-floor departmental switch.
3. **SW_GROUND_FLR** — Ground-floor departmental switch.

The floor switches connect departmental endpoints to the network. Trunk links transport the required VLANs between switches.

The DHCP server connects to the central switching infrastructure to provide DHCP services.

### 📸 EVE-NG Topology

Upload your topology screenshot to:

`topology/campus-network-topology.png`

Then embed it using:

![Campus Secure Network Topology](topology/campus-network-topology.png)

### 🔄 Logical Network Architecture

```mermaid
flowchart TB
    DHCP["DHCP_SERVER<br/>10.10.10.100"]

    MAIN["MAIN_SWITCH<br/>Central Switching<br/>VLAN Interfaces and DHCP Relay"]

    subgraph FLOOR1["First Floor"]
        SW1["SW_FLR1"]

        IT1["IT PC<br/>10.10.10.2"]
        HR["HR PC<br/>10.10.20.2"]
        MGMT["Management PC<br/>10.10.30.2"]
        CYBER1["Cybersecurity PC<br/>10.10.40.3"]

        SW1 --- IT1
        SW1 --- HR
        SW1 --- MGMT
        SW1 --- CYBER1
    end

    subgraph GROUND["Ground Floor"]
        SW2["SW_GROUND_FLR"]

        IT2["IT PC<br/>10.10.10.3"]
        CYBER2["Cybersecurity PC<br/>10.10.40.4"]
        FINANCE["Finance PC<br/>10.10.50.2"]
        MANAGER["Manager PC<br/>10.10.60.3"]

        SW2 --- IT2
        SW2 --- CYBER2
        SW2 --- FINANCE
        SW2 --- MANAGER
    end

    DHCP --- MAIN
    MAIN --- SW1
    MAIN --- SW2
```

> **Note:** This is a logical representation of the supplied topology. The actual interface connections and link types must match the EVE-NG lab.

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

### 📊 Device Summary

| Device Category | Count |
|---|---:|
| Switching devices | 3 |
| DHCP server/router node | 1 |
| Virtual PCs | 8 |
| **Total visible nodes** | **12** |

---

# 🔢 4. IP Addressing Plan

## 🌐 4.1 VLAN and Subnet Addressing

The campus network uses six VLANs to logically separate departments.

Each VLAN has its own IPv4 subnet and designated gateway address.

| VLAN ID | VLAN Name | Network Address | Subnet Mask | Default Gateway |
|---:|---|---|---|---|
| 10 | IT | `10.10.10.0/24` | `255.255.255.0` | `10.10.10.1` |
| 20 | HR | `10.10.20.0/24` | `255.255.255.0` | `10.10.20.1` |
| 30 | MGMT | `10.10.30.0/24` | `255.255.255.0` | `10.10.30.1` |
| 40 | CYBER | `10.10.40.0/24` | `255.255.255.0` | `10.10.40.1` |
| 50 | FINANCE | `10.10.50.0/24` | `255.255.255.0` | `10.10.50.1` |
| 60 | MANAGER | `10.10.60.0/24` | `255.255.255.0` | `10.10.60.1` |

## 💻 4.2 End-Device IP Addressing

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

## 📡 4.3 DHCP Server Addressing

| Parameter | Configuration |
|---|---|
| Device Name | `DHCP_SERVER` |
| Server IP Address | `10.10.10.100` |
| Connected Network | VLAN 10 / IT |
| DHCP Relay Device | `MAIN_SWITCH` |
| Helper Address | `10.10.10.100` |

**Important:** The gateway addresses above are the planned values from the addressing scheme. Confirm that each corresponding VLAN interface is configured and operational.

---

# 🔌 5. Interface Mapping

This section documents the visible interface labels from the EVE-NG topology.

Interface assignments should be confirmed against the running configuration and actual link connections before being treated as authoritative.

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

# 🛠️ 6. Technologies Used

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

# 🛠️ 7. Tasks to Perform

# 🔹 Phase 1 — Interface Configuration, VLANs and Switching

## 🎯 Objective

Configure all required VLANs, assign VLAN names, configure access and trunk ports, and implement DHCP relay on the central switch.

The phase is divided into three tasks corresponding to the switches in the topology.

---

## 📍 1.1 SW_FLR1 — First-Floor Switch Configuration

### 🎯 Objective

Configure the first-floor switch to provide network connectivity to IT, HR, Management, and Cybersecurity endpoints.

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

| Interface | Connected Endpoint | VLAN |
|---|---|---:|
| `Gi0/1` | `VPC1_VLAN10_IT` | 10 |
| `Gi0/2` | `VPC2_VLAN20_HR` | 20 |
| `Gi0/3` | `VPC3_VLAN30_MGMT` | 30 |
| `Gi1/0` | `VPC2_cyber_VLAN40` | 40 |
| Uplink | `MAIN_SWITCH` | Trunk |

*Confirm the uplink interface before applying configuration.*

### 🔍 Verification

```cisco
show vlan brief
show interfaces trunk
show interfaces status
show ip dhcp snooping
show cdp neighbors
```

### ✅ Expected Results

- Required VLANs exist.
- VLAN names are correct.
- Endpoints are assigned to the intended VLANs.
- The uplink carries the required VLANs.
- DHCP Snooping is configured according to the topology.

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

### 🔍 Verification

```cisco
show vlan brief
show interfaces trunk
show interfaces status
show ip dhcp snooping
show cdp neighbors
```

### ✅ Expected Results

- The required VLANs are created.
- Access interfaces are assigned correctly.
- The trunk link carries the required VLANs.
- Connected endpoints can reach their local gateways when the relevant gateway interfaces are operational.

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

### 🔍 Verification

```cisco
show vlan brief
show interfaces trunk
show ip interface brief
show running-config
show ip dhcp snooping
show ip dhcp snooping binding
```

### ⚠️ Configuration Considerations

- Confirm that the switch image supports Layer 3 routing and VLAN interfaces.
- Ensure that the relevant VLAN interfaces are operational.
- Ensure the DHCP server has the appropriate scopes and return connectivity.
- Trust only the interfaces that should receive legitimate DHCP server messages.
- Confirm that DHCP Snooping is supported by the selected device image.

### ✅ Expected Results

- All six VLANs are configured.
- VLAN interfaces use the planned gateway addresses.
- Trunk links transport the required VLANs.
- DHCP requests reach the centralized server through the relay.
- DHCP Snooping is configured and verified.

---

# 🔄 8. Project Implementation Roadmap

| Phase | Title | Main Tasks |
|---|---|---|
| Phase 1 | VLANs and Interface Configuration | VLANs, access ports, trunk links, VLAN interfaces, DHCP relay |
| Phase 2 | OSPF Routing | Dynamic routing, neighbor verification, route learning |
| Phase 3 | Centralized DHCP Relay | DHCP scopes, helper addresses, client address allocation |
| Phase 4 | SSH-Only Remote Management | SSH v2, local authentication, VTY restrictions |
| Phase 5 | Network Verification | Connectivity testing, troubleshooting, evidence collection |

---

# 🧪 9. Verification and Testing

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

# 📸 10. Project Screenshots

Organize the screenshots by implementation phase.

```text
screenshots/
├── phase-1-vlan-switching/
│   ├── eve-ng-topology.png
│   ├── vlan-configuration.png
│   ├── access-port-configuration.png
│   ├── trunk-configuration.png
│   ├── vlan-interface-addressing.png
│   └── dhcp-snooping.png
│
├── phase-2-ospf-routing/
│   ├── ospf-configuration.png
│   ├── ospf-neighbors.png
│   └── routing-table.png
│
├── phase-3-dhcp-relay/
│   ├── dhcp-server-configuration.png
│   ├── dhcp-relay-configuration.png
│   └── client-address-allocation.png
│
└── phase-4-ssh-management/
    ├── ssh-configuration.png
    ├── ssh-connectivity.png
    └── wireshark-ssh-capture.png
```

---

# 🏆 11. Project Outcomes

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

# 🔮 12. Future Enhancements

Potential improvements include:

- 🔒 Access Control Lists (ACLs) for inter-VLAN traffic restrictions.
- 🛡️ Port Security on appropriate access ports.
- 🔐 Dedicated management VLAN with restricted administrative access.
- 📊 Centralized logging and monitoring.
- ⏱️ Reliable NTP synchronization.
- 🧪 Additional failure testing and recovery documentation.
- 📈 Network monitoring and performance analysis.

---

## 👨‍💻 Author

**Project:** Campus Secure Network

**Platform:** EVE-NG

**Domain:** Enterprise Networking & Network Security

**Focus:** VLANs, Switching, DHCP, Routing, and Secure Management

---

> ⚠️ **Disclaimer:** This project is intended for educational and laboratory use. Validate all device configurations and security controls against the capabilities of the selected network images before applying them to a production network.
