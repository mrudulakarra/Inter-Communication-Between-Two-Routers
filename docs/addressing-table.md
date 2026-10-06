# Network Addressing Table

| Device  | Interface      | IP Address   | Subnet Mask     | Default Gateway |
|---------|----------------|--------------|-----------------|-----------------|
| Router0 | Fa0/0 (LAN)    | 192.168.1.1  | 255.255.255.0   | -               |
| Router0 | Fa0/1 (Link)   | 10.0.0.1     | 255.255.255.252 | -               |
| Router1 | Fa0/0 (LAN)    | 192.168.2.1  | 255.255.255.0   | -               |
| Router1 | Fa0/1 (Link)   | 10.0.0.2     | 255.255.255.252 | -               |
| PC0     | FastEthernet0  | 192.168.1.10 | 255.255.255.0   | 192.168.1.1     |
| PC1     | FastEthernet0  | 192.168.1.11 | 255.255.255.0   | 192.168.1.1     |
| Server0 | FastEthernet0  | 192.168.2.10 | 255.255.255.0   | 192.168.2.1     |
| Switch0 | VLAN 1 (optional) | 192.168.1.2 | 255.255.255.0 | 192.168.1.1     |
| Switch1 | VLAN 1 (optional) | 192.168.2.2 | 255.255.255.0 | 192.168.2.1     |

## Networks

| Network | CIDR | Usable hosts | Purpose |
|---|---|---|---|
| LAN 1 | 192.168.1.0/24 | 254 | PC0, PC1, Router0 |
| LAN 2 | 192.168.2.0/24 | 254 | Server0, Router1 |
| Inter-router link | 10.0.0.0/30 | 2 | Router0 <-> Router1 |

A /30 gives exactly 2 usable addresses (10.0.0.1 and 10.0.0.2), which is perfect for a link between two routers (network = 10.0.0.0, broadcast = 10.0.0.3).

## Cabling Plan

| From | Port | To | Port | Cable |
|---|---|---|---|---|
| PC0 | FastEthernet0 | Switch0 | Fa0/2 | Copper Straight-Through |
| PC1 | FastEthernet0 | Switch0 | Fa0/3 | Copper Straight-Through |
| Switch0 | Fa0/1 | Router0 | Fa0/0 | Copper Straight-Through |
| Router0 | Fa0/1 | Router1 | Fa0/1 | Copper Cross-Over |
| Router1 | Fa0/0 | Switch1 | Fa0/1 | Copper Straight-Through |
| Switch1 | Fa0/2 | Server0 | FastEthernet0 | Copper Straight-Through |

Rule: same-type devices (router-router) = cross-over; different-type devices (PC-switch, switch-router) = straight-through. Note: the 1841 ships with only 2 FastEthernet ports, which is exactly what this design uses.
