# IP Addressing Plan

Base enterprise block: `10.10.0.0/16`

The design uses separate /24 networks for user VLANs and branch LANs. WAN point-to-point links use /30 networks because only two usable IP addresses are needed per link.

## VLAN Subnets

| VLAN | Name | Network | Usable Range | Gateway |
|---:|---|---|---|---|
| 10 | HR | 10.10.10.0/24 | 10.10.10.1-10.10.10.254 | 10.10.10.1 |
| 20 | FINANCE | 10.10.20.0/24 | 10.10.20.1-10.10.20.254 | 10.10.20.1 |
| 30 | IT | 10.10.30.0/24 | 10.10.30.1-10.10.30.254 | 10.10.30.1 |
| 40 | SALES | 10.10.40.0/24 | 10.10.40.1-10.10.40.254 | 10.10.40.1 |
| 50 | MGMT | 10.10.50.0/24 | 10.10.50.1-10.10.50.254 | 10.10.50.1 |
| 60 | SERVERS | 10.10.60.0/24 | 10.10.60.1-10.10.60.254 | 10.10.60.1 |

## WAN Subnets

| Link | Network | Device A | Device B |
|---|---|---|---|
| CORE-HQ to R-HQ | 10.10.99.0/30 | CORE-HQ 10.10.99.1 | R-HQ 10.10.99.2 |
| R-HQ to R-ISP | 10.10.250.0/30 | R-HQ 10.10.250.1 | R-ISP 10.10.250.2 |
| R-ISP to R-BR1 | 10.10.250.4/30 | R-ISP 10.10.250.5 | R-BR1 10.10.250.6 |
| R-ISP to R-BR2 | 10.10.250.8/30 | R-ISP 10.10.250.9 | R-BR2 10.10.250.10 |

## Branch LANs

| Site | Network | Gateway |
|---|---|---|
| Branch 1 | 10.10.110.0/24 | 10.10.110.1 |
| Branch 2 | 10.10.120.0/24 | 10.10.120.1 |

## Static IP Recommendation

| Device | IP | Notes |
|---|---|---|
| Server | 10.10.60.10/24 | Default gateway 10.10.60.1 |
| ASW-HQ management | 10.10.50.11/24 | Default gateway 10.10.50.1 |
| ASW-BR1 management | 10.10.110.11/24 | Default gateway 10.10.110.1 |
| ASW-BR2 management | 10.10.120.11/24 | Default gateway 10.10.120.1 |
