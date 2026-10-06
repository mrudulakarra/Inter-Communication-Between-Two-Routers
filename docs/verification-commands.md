# Cisco Verification Commands and Expected Output

## show ip interface brief
Shows each interface, its IP, and its status. Both Status and Protocol should be `up`.
```
Router0# show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
FastEthernet0/0        192.168.1.1     YES manual up                    up
FastEthernet0/1        10.0.0.1        YES manual up                    up
Vlan1                  unassigned      YES unset  administratively down down
```
- `up/up` = working. `administratively down` = missing `no shutdown`. `down/down` = cable/neighbor problem.

## show ip route
Shows the routing table. Codes: C = connected, S = static.
```
Router0# show ip route
Gateway of last resort is not set

     10.0.0.0/30 is subnetted, 1 subnets
C       10.0.0.0 is directly connected, FastEthernet0/1
C    192.168.1.0/24 is directly connected, FastEthernet0/0
S    192.168.2.0/24 [1/0] via 10.0.0.2
```
Router1 shows the mirror image: `S 192.168.1.0/24 [1/0] via 10.0.0.1`.
`[1/0]` = administrative distance 1, metric 0 (normal for static routes).

## show running-config
Shows the active configuration in RAM. Check for: hostname, both interface IPs with `no shutdown` (no "shutdown" line), and the `ip route` line.
```
Router0# show running-config
hostname Router0
!
interface FastEthernet0/0
 description LAN1 - Connected to Switch0
 ip address 192.168.1.1 255.255.255.0
!
interface FastEthernet0/1
 description WAN - Point-to-Point Link to Router1
 ip address 10.0.0.1 255.255.255.252
!
ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

## show interfaces
Detailed per-interface info (status, MAC, IP, speed, errors). Useful: `show interfaces fastEthernet0/1`
```
FastEthernet0/1 is up, line protocol is up (connected)
  Hardware is Lance, address is 0001.xxxx.xxxx
  Description: WAN - Point-to-Point Link to Router1
  Internet address is 10.0.0.1/30
  Full-duplex, 100Mb/s
```

## ping
Tests reachability. `!` = reply received, `.` = timeout.
```
Router0# ping 10.0.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/4 ms
```
(First ping may show `.!!!!` due to ARP: 80% success is normal.)

## traceroute
Shows each router hop on the way.
```
Router0# traceroute 192.168.2.10
  1   10.0.0.2      1 msec  0 msec  0 msec
  2   192.168.2.10  1 msec  0 msec  0 msec
```
On a PC use `tracert`.

## Other helpful commands
| Command | Purpose |
|---|---|
| `show running-config \| section interface` | Only interface config |
| `show startup-config` | Saved config in NVRAM |
| `show cdp neighbors` | Which devices are connected to which ports |
| `show mac address-table` | (Switch) learned MAC addresses |
| `show vlan brief` | (Switch) ports in VLAN 1 |
