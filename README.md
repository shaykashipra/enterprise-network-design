# Enterprise Network Design with VLAN, Access/Core Switching & MPLS Architecture

## Overview

This project designs and simulates a small enterprise branch network using Cisco Packet Tracer. The working implementation focuses on VLANs, access/core switching, trunking, inter-VLAN routing, DHCP, OSPF-based WAN routing, and a basic ACL policy.

MPLS is included as an architecture and interview-focused concept only. Full provider-side MPLS, LDP, label switching, and MPLS VPN configuration are not implemented in Cisco Packet Tracer because standard Packet Tracer is not a realistic MPLS provider-core simulator.

## Problem Statement

A small organization has a Head Office and two branch offices. Departments at the Head Office need separate logical networks, centralized IP address assignment, controlled inter-department access, and routed connectivity to branch offices. The organization may later use an MPLS service provider for reliable enterprise WAN connectivity.

## Objectives

- Design a manageable enterprise network for a beginner-level Datacom project.
- Segment Head Office users using VLANs.
- Configure access ports for end devices.
- Configure a trunk link between access and core switches.
- Use a Layer-3 core switch for inter-VLAN routing.
- Provide DHCP services for user VLANs.
- Connect Head Office and two branches through a simulated WAN.
- Use OSPF area 0 for simple dynamic routing.
- Apply a basic ACL to demonstrate traffic control.
- Explain how MPLS PE/P/CE architecture would fit in a provider network.

## Technologies Used

- Cisco Packet Tracer
- Cisco 2960 access switches
- Cisco 3560 multilayer switch
- Cisco 2911 routers
- VLAN
- 802.1Q trunking
- Inter-VLAN routing using SVIs
- DHCP
- OSPF
- ACL
- IPv4 addressing and subnetting
- WAN simulation
- MPLS architecture, PE/P/CE concepts

## Network Topology

Logical topology:

![Enterprise network topology](topology/image.png)

## Device Roles

| Device | Suggested Packet Tracer model | Role |
|---|---:|---|
| ASW-HQ | 2960 | Head Office access switch for PCs/server |
| CORE-HQ | 3560 | Layer-3 core switch, SVIs, DHCP, ACL, OSPF |
| R-HQ | 2911 | Head Office WAN edge router |
| R-ISP | 2911 | Simulated provider/WAN router |
| R-BR1 | 2911 | Branch 1 router and DHCP server |
| R-BR2 | 2911 | Branch 2 router and DHCP server |
| ASW-BR1 | 2960 | Branch 1 access switch |
| ASW-BR2 | 2960 | Branch 2 access switch |

## VLAN Table

| VLAN ID | Name | Subnet | Default Gateway | Purpose |
|---:|---|---|---|---|
| 10 | HR | 10.10.10.0/24 | 10.10.10.1 | HR users |
| 20 | FINANCE | 10.10.20.0/24 | 10.10.20.1 | Finance users |
| 30 | IT | 10.10.30.0/24 | 10.10.30.1 | IT users |
| 40 | SALES | 10.10.40.0/24 | 10.10.40.1 | Sales users |
| 50 | MGMT | 10.10.50.0/24 | 10.10.50.1 | Switch management |
| 60 | SERVERS | 10.10.60.0/24 | 10.10.60.1 | Internal server network |

## IP Addressing Table

| Segment | Network | Gateway / Interface |
|---|---|---|
| HR VLAN | 10.10.10.0/24 | CORE-HQ VLAN 10: 10.10.10.1 |
| Finance VLAN | 10.10.20.0/24 | CORE-HQ VLAN 20: 10.10.20.1 |
| IT VLAN | 10.10.30.0/24 | CORE-HQ VLAN 30: 10.10.30.1 |
| Sales VLAN | 10.10.40.0/24 | CORE-HQ VLAN 40: 10.10.40.1 |
| Management VLAN | 10.10.50.0/24 | CORE-HQ VLAN 50: 10.10.50.1 |
| Server VLAN | 10.10.60.0/24 | CORE-HQ VLAN 60: 10.10.60.1 |
| Core to HQ Router | 10.10.99.0/30 | CORE-HQ: 10.10.99.1, R-HQ: 10.10.99.2 |
| HQ to Provider | 10.10.250.0/30 | R-HQ: 10.10.250.1, R-ISP: 10.10.250.2 |
| Provider to Branch 1 | 10.10.250.4/30 | R-ISP: 10.10.250.5, R-BR1: 10.10.250.6 |
| Provider to Branch 2 | 10.10.250.8/30 | R-ISP: 10.10.250.9, R-BR2: 10.10.250.10 |
| Branch 1 LAN | 10.10.110.0/24 | R-BR1: 10.10.110.1 |
| Branch 2 LAN | 10.10.120.0/24 | R-BR2: 10.10.120.1 |

## Switching

The Head Office access switch connects end devices using access ports. Each access port belongs to exactly one VLAN. The access switch does not route between VLANs; it only forwards Layer-2 frames inside VLANs.

## Trunking

The link between ASW-HQ and CORE-HQ is an 802.1Q trunk. A trunk is required because traffic from multiple VLANs must travel over one physical link toward the Layer-3 core switch.

## Inter-VLAN Routing

CORE-HQ uses switched virtual interfaces, also called SVIs, as default gateways for VLANs. Example: VLAN 10 users use 10.10.10.1 as their gateway. When HR needs to reach the server VLAN or a branch LAN, the core switch routes the traffic at Layer 3.

## DHCP

CORE-HQ provides DHCP for Head Office VLANs. Branch routers provide DHCP for their local branch LANs. The server VLAN uses a static IP address for the internal server.

## Routing And OSPF

OSPF area 0 is used between CORE-HQ, R-HQ, R-ISP, R-BR1, and R-BR2. OSPF keeps the routing design simple and avoids manually adding many static routes.

## ACL Policy

A simple ACL is applied on CORE-HQ to block HR users from reaching Finance users. Other traffic is allowed. This demonstrates basic policy enforcement without overcomplicating the project.

| Policy | Source | Destination | Action |
|---|---|---|---|
| HR cannot access Finance | 10.10.10.0/24 | 10.10.20.0/24 | Deny |
| Other traffic | Any | Any | Permit |

## WAN

The WAN is simulated using routers and point-to-point /30 networks. R-ISP represents a provider/WAN network. In a real enterprise, this provider network could be an MPLS service provider, leased line, or other managed WAN.

## MPLS Architecture

MPLS is not configured in the Packet Tracer implementation. It is explained conceptually as the provider technology that can connect enterprise sites.

In an MPLS design:

- CE means Customer Edge router. In this project, R-HQ, R-BR1, and R-BR2 act like enterprise CE routers.
- PE means Provider Edge router. PE routers connect customer sites to the provider network.
- P means Provider core router. P routers forward labeled packets inside the provider network.
- MPLS forwarding uses labels instead of only destination IP lookup at every hop.

Packet Tracer limitation: this project uses Packet Tracer for VLAN, switching, routing, OSPF, ACL, DHCP, and WAN simulation. Full MPLS provider-core implementation is outside the realistic capabilities of standard Packet Tracer.

## Testing

Recommended tests:

| Test | Source | Destination | Expected Result |
|---|---|---|---|
| Same VLAN gateway | HR PC | 10.10.10.1 | Success |
| Inter-VLAN allowed | IT PC | Server 10.10.60.10 | Success |
| ACL blocked | HR PC | Finance PC | Fail |
| Branch 1 reach HQ | Branch 1 PC | Server 10.10.60.10 | Success |
| Branch 2 reach HQ | Branch 2 PC | Server 10.10.60.10 | Success |
| Branch to branch | Branch 1 PC | Branch 2 PC | Success |
| OSPF | Routers/Core | show ip ospf neighbor | Neighbor up |
| Routing table | Routers/Core | show ip route | OSPF routes visible |

## Screenshots Checklist

Add screenshots in the `topology/` folder:

1. Full topology
2. VLAN configuration: `show vlan brief`
3. Trunk configuration: `show interfaces trunk`
4. IP interface status: `show ip interface brief`
5. Routing table: `show ip route`
6. OSPF neighbors: `show ip ospf neighbor`
7. Successful ping from HQ to branch
8. ACL blocked ping from HR to Finance
9. Simulation Mode packet flow

## Limitations

- Full MPLS provider-core configuration is not implemented in Packet Tracer.
- No BGP is used because it is unnecessary for this beginner-friendly enterprise project.
- No redundancy, STP tuning, HSRP, or QoS is implemented.
- Security is limited to one simple ACL for demonstration.

## Future Improvements

- Add redundant access-to-core links.
- Add STP verification and root bridge planning.
- Add NAT and an internet simulation segment.
- Build an optional MPLS lab in EVE-NG or GNS3.
- Convert the topology to Huawei eNSP/VRP-style configuration for vendor comparison.

## Learning Outcomes

After completing this project, I can explain how VLANs separate users, how trunking carries multiple VLANs, how a Layer-3 core switch routes between VLANs, how DHCP assigns IP addresses, how OSPF shares routes, how ACLs control traffic, and how MPLS can fit into an enterprise WAN architecture.
