# Intercommunication Between Two Routers

> A Cisco Packet Tracer networking project demonstrating communication between two separate LANs through two Cisco 1841 routers connected by a point-to-point link and configured with static routing.

## Project Overview

Intercommunication Between Two Routers is a Cisco Packet Tracer networking project that demonstrates communication between two separate LAN networks using two Cisco 1841 routers. The routers are connected through a point-to-point network and configured with static routing, allowing end devices in one LAN to communicate with devices in another LAN.

## Project Objective

Design and configure a network where LAN 1 (PC0, PC1) and LAN 2 (Server0) communicate successfully through Router0 and Router1. The project demonstrates:

1. IP addressing  2. Router interface configuration  3. Switch connectivity  4. Inter-router communication
5. Static routing  6. LAN-to-LAN communication  7. Ping testing  8. Troubleshooting  9. Basic Cisco IOS commands

## Network Topology

```
PC0 ──┐
      │
PC1 ──┤
      │
   Switch0
      │
   Router0  (Fa0/0 = 192.168.1.1)
      │      (Fa0/1 = 10.0.0.1)
  10.0.0.0/30
      │      (Fa0/1 = 10.0.0.2)
   Router1  (Fa0/0 = 192.168.2.1)
      │
   Switch1
      │
   Server0
```

## Devices Used

| Qty | Device | Model | Names |
|---|---|---|---|
| 2 | Router | Cisco 1841 | Router0, Router1 |
| 2 | Switch | Cisco 2950-24 | Switch0, Switch1 |
| 2 | PC | Generic PC | PC0, PC1 |
| 1 | Server | Generic Server | Server0 |

## IP Addressing

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| Router0 | Fa0/0 (LAN) | 192.168.1.1 | 255.255.255.0 | - |
| Router0 | Fa0/1 (Router Link) | 10.0.0.1 | 255.255.255.252 | - |
| Router1 | Fa0/0 (LAN) | 192.168.2.1 | 255.255.255.0 | - |
| Router1 | Fa0/1 (Router Link) | 10.0.0.2 | 255.255.255.252 | - |
| PC0 | FastEthernet0 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC1 | FastEthernet0 | 192.168.1.11 | 255.255.255.0 | 192.168.1.1 |
| Server0 | FastEthernet0 | 192.168.2.10 | 255.255.255.0 | 192.168.2.1 |

Optional switch management IPs: Switch0 = 192.168.1.2, Switch1 = 192.168.2.2 (VLAN 1). See [docs/addressing-table.md](docs/addressing-table.md) for the cabling plan.

## Network Architecture

- **LAN 1 (192.168.1.0/24):** PC0 and PC1 connect to Switch0, which connects to Router0 Fa0/0.
- **LAN 2 (192.168.2.0/24):** Server0 connects to Switch1, which connects to Router1 Fa0/0.
- **Inter-router link (10.0.0.0/30):** Router0 Fa0/1 <-> Router1 Fa0/1 using a copper cross-over cable. A /30 provides exactly two usable addresses.
- **Layer 2** (switches) forwards traffic inside a LAN; **Layer 3** (routers) forwards traffic between networks.

## Router Configuration

Full files: [configs/Router0.txt](configs/Router0.txt), [configs/Router1.txt](configs/Router1.txt). Every command is explained in [docs/command-explanations.md](docs/command-explanations.md).

**Router0**
```
enable
configure terminal
hostname Router0
interface FastEthernet0/0
 description LAN1 - Connected to Switch0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
 exit
interface FastEthernet0/1
 description WAN - Point-to-Point Link to Router1
 ip address 10.0.0.1 255.255.255.252
 no shutdown
 exit
ip route 192.168.2.0 255.255.255.0 10.0.0.2
end
write memory
```

**Router1**
```
enable
configure terminal
hostname Router1
interface FastEthernet0/0
 description LAN2 - Connected to Switch1
 ip address 192.168.2.1 255.255.255.0
 no shutdown
 exit
interface FastEthernet0/1
 description WAN - Point-to-Point Link to Router0
 ip address 10.0.0.2 255.255.255.252
 no shutdown
 exit
ip route 192.168.1.0 255.255.255.0 10.0.0.1
end
write memory
```

## Switch Configuration

Files: [configs/Switch0.txt](configs/Switch0.txt), [configs/Switch1.txt](configs/Switch1.txt). The 2950 switches forward traffic with default settings (all ports in VLAN 1). The configs add hostnames, port descriptions and an optional management IP. End-device settings are in [configs/End-Devices.txt](configs/End-Devices.txt).

## Static Routing

| Router | Destination | Mask | Next Hop | Meaning |
|---|---|---|---|---|
| Router0 | 192.168.2.0 | 255.255.255.0 | 10.0.0.2 | Reach LAN 2 via Router1 |
| Router1 | 192.168.1.0 | 255.255.255.0 | 10.0.0.1 | Reach LAN 1 via Router0 |

Each router only knows its directly connected networks. Static routes teach each router about the remote LAN so packets and their replies can travel both ways.

## Testing

| Test | Source | Destination | Command |
|---|---|---|---|
| 1 | PC0 | PC1 | `ping 192.168.1.11` |
| 2 | PC0 | Router0 | `ping 192.168.1.1` |
| 3 | PC0 | Router1 | `ping 10.0.0.2` / `ping 192.168.2.1` |
| 4 | PC0 | Server0 | `ping 192.168.2.10` |
| 5 | PC1 | Server0 | `ping 192.168.2.10` |
| 6 | Server0 | PC0 | `ping 192.168.1.10` |

Verification commands: `show ip interface brief`, `show ip route`, `show running-config`, `show interfaces`, `ping`, `traceroute`. Details and sample output: [docs/testing-procedures.md](docs/testing-procedures.md), [docs/verification-commands.md](docs/verification-commands.md).

## Expected Results

- All six tests succeed (the first ping may time out once because of ARP; this is normal).
- Pings between LANs show `TTL=126` (two routers crossed).
- `show ip interface brief` shows `up / up` on all used interfaces.
- `show ip route` on Router0 contains `C 192.168.1.0/24`, `C 10.0.0.0/30` and `S 192.168.2.0/24 via 10.0.0.2` (mirror image on Router1).
- `tracert 192.168.2.10` from PC0 shows hops 192.168.1.1 -> 10.0.0.2 -> 192.168.2.10.

## Troubleshooting

Covers: interface administratively down, wrong IP, wrong mask, wrong gateway, missing static route, incorrect cable, unconnected router interfaces, PC cannot ping another network, router cannot ping its neighbor. See [docs/troubleshooting.md](docs/troubleshooting.md).

## How to Build in Packet Tracer

1. Place 2 x 1841 routers, 2 x 2950-24 switches, 2 PCs and 1 server.
2. Cable the devices using the cabling plan (straight-through for PC-switch and switch-router; cross-over router-to-router).
3. Set IPs on PC0, PC1 and Server0 (see `configs/End-Devices.txt`).
4. Paste each file from `configs/` into the matching device CLI.
5. Open or run the pre-configured Packet Tracer file `2router.pkt` directly in Cisco Packet Tracer, or build it manually using these instructions.

## Technologies Used

- Cisco Packet Tracer
- Cisco IOS CLI
- IPv4 addressing and subnetting (/24, /30)
- Static routing
- ICMP (ping, traceroute)
- Ethernet / Layer 2 switching

## Learning Outcomes

- Plan an IPv4 addressing scheme with subnetting
- Configure router interfaces and static routes
- Understand Layer 2 vs Layer 3 forwarding
- Verify networks with `show` commands, ping and traceroute
- Diagnose and fix common connectivity faults
- Document a network professionally

## Project Structure

```
Intercommunication-Between-Two-Routers/
├── 2router.pkt
├── README.md
├── configs/   Router0.txt  Router1.txt  Switch0.txt  Switch1.txt  End-Devices.txt
├── docs/      addressing-table.md  command-explanations.md  testing-procedures.md
│              verification-commands.md  troubleshooting.md
└── images/    topology.png
```

## Conclusion

This project shows how two independent LANs can be joined using two routers, a dedicated /30 point-to-point link and static routes. It builds core skills (addressing, routing, verification, troubleshooting) that underpin larger enterprise networks, and is a good base for extensions such as RIP/OSPF, ACLs for security, VLANs, or NAT.
