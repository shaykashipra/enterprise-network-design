# MPLS Architecture

## What MPLS Is

MPLS means Multiprotocol Label Switching. It is a provider-network technology that forwards traffic using short labels instead of performing a full IP routing-table lookup at every provider hop.

## Why Service Providers Use MPLS

Service providers use MPLS because it helps them carry traffic for many customers efficiently. MPLS can support traffic engineering, VPN separation, predictable forwarding, and scalable enterprise WAN services.

## Label Switching Concept

In normal IP routing, each router checks the destination IP address and decides the next hop.

In MPLS, the provider edge router adds a label. Provider routers then forward packets based on labels. Near the exit, the label is removed and the packet is delivered toward the customer site.

## FEC

FEC means Forwarding Equivalence Class. It is a group of packets that receive the same forwarding treatment inside the MPLS network.

## PE, P, And CE

| Term | Meaning | Role |
|---|---|---|
| CE | Customer Edge | Enterprise router connected to the provider |
| PE | Provider Edge | Provider router connected to customer CE routers |
| P | Provider | Provider core router that forwards labeled traffic |

In this Packet Tracer project:

- R-HQ, R-BR1, and R-BR2 are similar to CE routers.
- R-ISP represents the provider/WAN cloud in a simplified way.
- Real PE/P/LDP/MPLS VPN behavior is conceptual only.

## Enterprise-To-Provider Connectivity

In a real MPLS WAN:

```text
HQ CE -- PE -- P -- PE -- Branch CE
```

The enterprise normally does not control the provider core. The enterprise connects its CE router to the service provider PE router. The provider handles MPLS forwarding inside its network.

## IP Routing vs MPLS Forwarding

| Topic | Normal IP Routing | MPLS Forwarding |
|---|---|---|
| Forwarding decision | Destination IP lookup | Label lookup |
| Where used | Enterprise and internet routing | Mostly service provider core |
| Customer visibility | Customer manages routes | Provider core is usually hidden |
| Packet marker | IP header | MPLS label stack |

## Packet Tracer Limitation

Packet Tracer limitation: The project uses Packet Tracer for VLAN, switching, routing, OSPF, ACL, DHCP, and WAN simulation. Full MPLS provider-core implementation is outside the realistic capabilities of standard Packet Tracer.

## Optional Advanced Extension

If you later use EVE-NG or GNS3, a small MPLS lab could include:

```text
CE-HQ -- PE1 -- P1 -- PE2 -- CE-BR1
```

Possible advanced topics:

- Provider IGP such as OSPF or IS-IS
- MPLS enabled on PE/P links
- LDP neighbor formation
- VRF on PE routers
- MP-BGP between PE routers
- MPLS L3VPN route exchange

This optional lab is not required for completing or defending the main Packet Tracer project.
