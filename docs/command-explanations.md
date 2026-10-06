# Command Explanations (Beginner-Friendly)

| Command | Meaning in simple words |
|---|---|
| `enable` | Move from User EXEC (`>`) to Privileged EXEC (`#`) mode. Like logging in as admin. |
| `configure terminal` | Enter Global Configuration mode, where you can change settings. |
| `hostname Router0` | Give the device a name so you can tell devices apart. |
| `no ip domain-lookup` | Stops the router trying to resolve mistyped commands as website names (avoids long freezes). |
| `interface FastEthernet0/0` | Select a port to configure. Prompt changes to `(config-if)#`. |
| `description ...` | A note attached to a port. It does not affect traffic, but makes the network easy to document. |
| `ip address 192.168.1.1 255.255.255.0` | Give the interface an IP and subnet mask. This becomes the gateway for LAN 1. |
| `no shutdown` | Turn the port ON. Router ports are OFF by default. |
| `exit` | Go back one level (interface -> global config). |
| `ip route 192.168.2.0 255.255.255.0 10.0.0.2` | Static route: "To reach network 192.168.2.0/24, send packets to 10.0.0.2 (next hop)." |
| `line console 0` / `logging synchronous` | Stops system messages from cutting into the line you are typing. |
| `end` | Jump straight back to Privileged EXEC mode (`#`). |
| `write memory` (or `copy running-config startup-config`) | Save settings to NVRAM so they survive a reboot. |
| `interface Vlan1` (switch) | The switch's virtual management interface. |
| `ip default-gateway 192.168.1.1` (switch) | Lets the switch reply to devices on other networks (management only). |

## Why static routes are needed
A router only knows networks directly attached to it. Router0 knows 192.168.1.0/24 and 10.0.0.0/30 but has never heard of 192.168.2.0/24, so it would drop packets for Server0. The static route teaches it where to send them. Both routers need a route (the return path), otherwise pings reach the destination but replies are lost.

## Why switches need very little configuration
A switch works at Layer 2 using MAC addresses. Out of the box all ports are in VLAN 1 and enabled, so PC-to-PC traffic within a LAN works automatically. The router handles all traffic between LANs (Layer 3).

## Packet journey: PC0 -> Server0
1. PC0 sees 192.168.2.10 is outside its network, so it sends the packet to its gateway 192.168.1.1 (MAC found via ARP).
2. Switch0 forwards the frame to Router0.
3. Router0 checks its table: 192.168.2.0/24 via 10.0.0.2, forwards out Fa0/1.
4. Router1 receives it, 192.168.2.0/24 is directly connected, so it delivers via Fa0/0.
5. Switch1 forwards to Server0, which replies along the reverse path.
