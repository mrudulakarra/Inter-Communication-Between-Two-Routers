# Troubleshooting Guide

General method: check bottom-up. Cables/link lights -> interface status -> IP settings -> routing -> end devices.

| Problem | Symptom | How to Detect | Solution |
|---|---|---|---|
| Interface administratively down | Port shows red/down, `administratively down` | `show ip interface brief` | `interface fa0/0` then `no shutdown` |
| Wrong IP address | Ping fails, wrong network in route table | `show ip interface brief`, PC IP Configuration | Re-enter correct IP, e.g. `ip address 192.168.1.1 255.255.255.0` |
| Wrong subnet mask | Some hosts reachable, others not; odd route entries | `show running-config`, IP Configuration | Fix mask: LANs = 255.255.255.0, link = 255.255.255.252 |
| Incorrect default gateway | Same-LAN ping works, other-LAN ping fails | PC > `ipconfig` | Set gateway to the router LAN IP (192.168.1.1 or 192.168.2.1) |
| Missing static route | One-way: reaches far router but no reply, or "Destination host unreachable" from a router | `show ip route` lacks `S` entry | `ip route 192.168.2.0 255.255.255.0 10.0.0.2` (Router0), `ip route 192.168.1.0 255.255.255.0 10.0.0.1` (Router1) |
| Incorrect cable | Link lights stay red / no link | Check cable type in PT | Straight-through for PC-switch and switch-router; cross-over for router-router |
| Router interfaces not connected | `down/down` status | `show ip interface brief`, `show cdp neighbors` | Connect Fa0/1 <-> Fa0/1, check both ends are `no shutdown` |
| PC cannot ping another network | Local OK, remote fails | `ipconfig`, `ping gateway`, `tracert` | Check gateway -> then routes on both routers -> then far-end gateway |
| Router cannot ping neighboring router | `.....` result | `show ip interface brief` on both | Check cable, `no shutdown` on both, same /30 subnet (10.0.0.1 and 10.0.0.2) |

## Worked example: PC0 cannot ping Server0
1. `ping 192.168.1.1` from PC0 fails -> check PC0 IP/gateway and cable to Switch0.
2. Router0: `ping 10.0.0.2` fails -> check inter-router cable and `no shutdown`.
3. Router0: `show ip route` has no `S 192.168.2.0` -> add the static route.
4. Router1: `ping 192.168.1.10` fails -> Router1 lacks return route; add it.
5. Server0 gateway wrong -> set to 192.168.2.1.

## Handy debugging commands
`show ip interface brief`, `show ip route`, `show running-config`, `show cdp neighbors`, `ping`, `traceroute`, `ipconfig /all` (PC), `arp -a` (PC).
In Packet Tracer, use Simulation Mode and filter on ICMP to watch each packet hop.

## Common typing mistakes
- `10.0.0.2` vs `10.0.2.0`
- Mask `255.255.255.0` on the /30 link (should be `255.255.255.252`)
- Forgetting `write memory` before closing (config lost on reload)
- Putting both ends of the link on the same IP
