# Network Architecture

## Design Summary

This project uses a small hierarchical enterprise design:

- Access layer: connects end devices.
- Core/distribution layer: performs inter-VLAN routing and central policy control.
- WAN edge: connects the Head Office to branch offices through a simulated provider network.

The design is intentionally compact so it can be built and defended by a networking beginner within 2-3 days.

## Packet Tracer Device List

| Device Name | Model | Quantity | Purpose |
|---|---:|---:|---|
| ASW-HQ | 2960 | 1 | Head Office access switching |
| CORE-HQ | 3560 | 1 | VLAN gateways, routing, DHCP, ACL |
| R-HQ | 2911 | 1 | Head Office WAN edge |
| R-ISP | 2911 | 1 | Simulated provider/WAN |
| R-BR1 | 2911 | 1 | Branch 1 router |
| R-BR2 | 2911 | 1 | Branch 2 router |
| ASW-BR1 | 2960 | 1 | Branch 1 access switching |
| ASW-BR2 | 2960 | 1 | Branch 2 access switching |
| PCs | PC | 6+ | Department and branch users |
| Server | Server | 1 | Internal service test host |

## Port Mapping

### Head Office

| From Device | Port | To Device | Port | Link Type |
|---|---|---|---|---|
| HR-PC | FastEthernet0 | ASW-HQ | Fa0/1 | Access VLAN 10 |
| FIN-PC | FastEthernet0 | ASW-HQ | Fa0/2 | Access VLAN 20 |
| IT-PC | FastEthernet0 | ASW-HQ | Fa0/3 | Access VLAN 30 |
| SALES-PC | FastEthernet0 | ASW-HQ | Fa0/4 | Access VLAN 40 |
| SERVER1 | FastEthernet0 | ASW-HQ | Fa0/5 | Access VLAN 60 |
| ASW-HQ | Fa0/24 | CORE-HQ | Fa0/1 | Trunk |
| CORE-HQ | Gi0/1 | R-HQ | Gi0/0 | Routed link |

### WAN And Branches

| From Device | Port | To Device | Port | Network |
|---|---|---|---|---|
| R-HQ | Gi0/1 | R-ISP | Gi0/0 | 10.10.250.0/30 |
| R-ISP | Gi0/1 | R-BR1 | Gi0/0 | 10.10.250.4/30 |
| R-ISP | Gi0/2 | R-BR2 | Gi0/0 | 10.10.250.8/30 |
| R-BR1 | Gi0/1 | ASW-BR1 | Fa0/24 | Branch 1 LAN |
| R-BR2 | Gi0/1 | ASW-BR2 | Fa0/24 | Branch 2 LAN |
| BR1-PC | FastEthernet0 | ASW-BR1 | Fa0/1 | Access |
| BR2-PC | FastEthernet0 | ASW-BR2 | Fa0/1 | Access |

## Packet Tracer Build Guide

### STEP 1 - Create The Topology

DEVICE: Packet Tracer workspace

PURPOSE: Place all devices and connect them correctly.

Use these devices:

- One 2960 switch named ASW-HQ
- One 3560 switch named CORE-HQ
- Four 2911 routers named R-HQ, R-ISP, R-BR1, R-BR2
- Two 2960 switches named ASW-BR1 and ASW-BR2
- Six PCs: HR-PC, FIN-PC, IT-PC, SALES-PC, BR1-PC, BR2-PC
- One Server named SERVER1

Use Copper Straight-Through cables for PC-to-switch, switch-to-router, and router-to-router Ethernet links in Packet Tracer. If Packet Tracer shows an automatic cable option, Auto-Connect is acceptable for this beginner project.

EXPECTED RESULT: Link lights should eventually turn green. Some switch links may take a short time because of Layer-2 startup behavior.

COMMON ERROR: Choosing a router with too few Gigabit interfaces. Use 2911 routers so R-ISP can connect to HQ, Branch 1, and Branch 2.

### STEP 2 - Configure Device Names

DEVICE: Each router and switch

PURPOSE: Make the topology readable and professional.

COMMANDS:

```text
enable
configure terminal
hostname DEVICE-NAME
end
copy running-config startup-config
```

Replace `DEVICE-NAME` with ASW-HQ, CORE-HQ, R-HQ, R-ISP, R-BR1, R-BR2, ASW-BR1, or ASW-BR2.

VERIFICATION:

```text
show running-config
```

COMMON ERROR: Configuring the wrong device. Check the prompt before entering commands.

### STEP 3 - Create VLANs

DEVICE: ASW-HQ and CORE-HQ

PURPOSE: VLANs separate Head Office departments into different broadcast domains.

COMMANDS:

```text
enable
configure terminal
vlan 10
 name HR
vlan 20
 name FINANCE
vlan 30
 name IT
vlan 40
 name SALES
vlan 50
 name MGMT
vlan 60
 name SERVERS
end
```

VERIFICATION:

```text
show vlan brief
```

EXPECTED RESULT: VLANs 10, 20, 30, 40, 50, and 60 appear in the VLAN table.

COMMON ERROR: Creating VLANs only on one switch. Create them on both ASW-HQ and CORE-HQ.

### STEP 4 - Configure Access Ports

DEVICE: ASW-HQ

PURPOSE: Assign each end-device port to the correct VLAN.

COMMANDS:

```text
enable
configure terminal
interface fa0/1
 description HR-PC
 switchport mode access
 switchport access vlan 10
interface fa0/2
 description FIN-PC
 switchport mode access
 switchport access vlan 20
interface fa0/3
 description IT-PC
 switchport mode access
 switchport access vlan 30
interface fa0/4
 description SALES-PC
 switchport mode access
 switchport access vlan 40
interface fa0/5
 description SERVER1
 switchport mode access
 switchport access vlan 60
end
```

VERIFICATION:

```text
show vlan brief
```

EXPECTED RESULT: Fa0/1 appears under VLAN 10, Fa0/2 under VLAN 20, Fa0/3 under VLAN 30, Fa0/4 under VLAN 40, and Fa0/5 under VLAN 60.

COMMON ERROR: Assigning the wrong switch port. Confirm cable connections before configuring.

### STEP 5 - Configure The Trunk

DEVICE: ASW-HQ and CORE-HQ

PURPOSE: Carry multiple VLANs between access and core switches.

ASW-HQ COMMANDS:

```text
enable
configure terminal
interface fa0/24
 description TRUNK_TO_CORE-HQ
 switchport mode trunk
end
```

CORE-HQ COMMANDS:

```text
enable
configure terminal
interface fa0/1
 description TRUNK_TO_ASW-HQ
 switchport trunk encapsulation dot1q
 switchport mode trunk
end
```

Note: Some Packet Tracer switch models do not require or support `switchport trunk encapsulation dot1q`. If the command is rejected, continue with `switchport mode trunk`.

VERIFICATION:

```text
show interfaces trunk
```

EXPECTED RESULT: The trunk port appears as trunking and VLANs are allowed.

COMMON ERROR: Trunk configured only on one side. Configure both ends.

### STEP 6 - Configure Core Layer-3 Switch

DEVICE: CORE-HQ

PURPOSE: Enable Layer-3 routing and create gateway interfaces for VLANs.

COMMANDS:

```text
enable
configure terminal
ip routing
interface vlan 10
 description HR_GATEWAY
 ip address 10.10.10.1 255.255.255.0
 no shutdown
interface vlan 20
 description FINANCE_GATEWAY
 ip address 10.10.20.1 255.255.255.0
 no shutdown
interface vlan 30
 description IT_GATEWAY
 ip address 10.10.30.1 255.255.255.0
 no shutdown
interface vlan 40
 description SALES_GATEWAY
 ip address 10.10.40.1 255.255.255.0
 no shutdown
interface vlan 50
 description MGMT_GATEWAY
 ip address 10.10.50.1 255.255.255.0
 no shutdown
interface vlan 60
 description SERVER_GATEWAY
 ip address 10.10.60.1 255.255.255.0
 no shutdown
interface gi0/1
 description ROUTED_LINK_TO_R-HQ
 no switchport
 ip address 10.10.99.1 255.255.255.252
 no shutdown
end
```

VERIFICATION:

```text
show ip interface brief
show ip route
```

EXPECTED RESULT: VLAN interfaces and Gi0/1 are up/up. Connected routes appear in the routing table.

COMMON ERROR: SVI stays down. At least one active access/trunk port in that VLAN must be up.

### STEP 7 - Configure DHCP

DEVICE: CORE-HQ

PURPOSE: Automatically assign IP addresses to Head Office PCs.

COMMANDS:

```text
enable
configure terminal
ip dhcp excluded-address 10.10.10.1 10.10.10.20
ip dhcp excluded-address 10.10.20.1 10.10.20.20
ip dhcp excluded-address 10.10.30.1 10.10.30.20
ip dhcp excluded-address 10.10.40.1 10.10.40.20
ip dhcp excluded-address 10.10.50.1 10.10.50.20

ip dhcp pool HR
 network 10.10.10.0 255.255.255.0
 default-router 10.10.10.1
 dns-server 8.8.8.8
ip dhcp pool FINANCE
 network 10.10.20.0 255.255.255.0
 default-router 10.10.20.1
 dns-server 8.8.8.8
ip dhcp pool IT
 network 10.10.30.0 255.255.255.0
 default-router 10.10.30.1
 dns-server 8.8.8.8
ip dhcp pool SALES
 network 10.10.40.0 255.255.255.0
 default-router 10.10.40.1
 dns-server 8.8.8.8
end
```

Set HQ PCs to DHCP from Desktop > IP Configuration.

Set SERVER1 manually:

- IP: 10.10.60.10
- Mask: 255.255.255.0
- Gateway: 10.10.60.1

VERIFICATION:

```text
show ip dhcp binding
```

COMMON ERROR: PC receives APIPA address 169.254.x.x. Check VLAN, trunk, and DHCP pool.

### STEP 8 - Configure WAN And Branch Interfaces

DEVICE: R-HQ, R-ISP, R-BR1, R-BR2

PURPOSE: Create routed links between sites.

Use the router configuration files in `configs/` for exact commands.

VERIFICATION:

```text
show ip interface brief
ping 10.10.250.2
```

COMMON ERROR: Interface is administratively down. Use `no shutdown`.

### STEP 9 - Configure OSPF

DEVICE: CORE-HQ, R-HQ, R-ISP, R-BR1, R-BR2

PURPOSE: Dynamically advertise HQ and branch routes.

Use OSPF process 1 and area 0.

VERIFICATION:

```text
show ip ospf neighbor
show ip route
```

EXPECTED RESULT: Neighbor relationships form between directly connected Layer-3 devices. Remote routes appear with `O` in the routing table.

COMMON ERROR: Wrong network wildcard mask. Use the exact configs in `configs/`.

### STEP 10 - Configure ACL

DEVICE: CORE-HQ

PURPOSE: Block HR from Finance while allowing other traffic.

COMMANDS:

```text
enable
configure terminal
ip access-list extended HR_BLOCK_FINANCE
 deny ip 10.10.10.0 0.0.0.255 10.10.20.0 0.0.0.255
 permit ip any any
interface vlan 10
 ip access-group HR_BLOCK_FINANCE in
end
```

VERIFICATION:

```text
show access-lists
```

Test from HR-PC to FIN-PC. The ping should fail. Then test HR-PC to SERVER1. It should succeed.

COMMON ERROR: Forgetting `permit ip any any`. Without it, the ACL denies all other traffic.

### STEP 11 - Save Configurations

DEVICE: Every router and switch

COMMANDS:

```text
copy running-config startup-config
```

EXPECTED RESULT:

```text
Destination filename [startup-config]?
Building configuration...
[OK]
```

Press Enter when asked for the destination filename.
