# Testing Procedures

Run from PC: Desktop > Command Prompt. Run from Server: Desktop > Command Prompt.
Note: the first ping often shows 1 timeout (`Request timed out.`) because ARP is still resolving. Ping again; the rest should reply.

| # | Source | Destination | Command | Expected Result |
|---|--------|-------------|---------|-----------------|
| 1 | PC0 | PC1 | `ping 192.168.1.11` | Success (same LAN, via Switch0) |
| 2 | PC0 | Router0 | `ping 192.168.1.1` | Success (default gateway) |
| 3a | PC0 | Router1 (link) | `ping 10.0.0.2` | Success (via Router0) |
| 3b | PC0 | Router1 (LAN) | `ping 192.168.2.1` | Success (via Router0 -> Router1) |
| 4 | PC0 | Server0 | `ping 192.168.2.10` | Success (LAN1 -> LAN2) |
| 5 | PC1 | Server0 | `ping 192.168.2.10` | Success |
| 6 | Server0 | PC0 | `ping 192.168.1.10` | Success (reverse path via static route) |

## Example output (Test 4)

```
C:\> ping 192.168.2.10

Pinging 192.168.2.10 with 32 bytes of data:

Request timed out.           <- first packet: ARP
Reply from 192.168.2.10: bytes=32 time=2ms TTL=126
Reply from 192.168.2.10: bytes=32 time=1ms TTL=126
Reply from 192.168.2.10: bytes=32 time=1ms TTL=126

Ping statistics for 192.168.2.10:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss)
```

TTL=126 is correct: the packet started at 128 and passed through 2 routers.

## Trace the path

```
C:\> tracert 192.168.2.10
  1   1 ms   0 ms   0 ms   192.168.1.1     (Router0)
  2   1 ms   1 ms   0 ms   10.0.0.2        (Router1)
  3   1 ms   0 ms   1 ms   192.168.2.10    (Server0)
```

## Router-to-router tests
```
Router0# ping 10.0.0.2
Router0# ping 192.168.2.10
Router1# ping 10.0.0.1
Router1# ping 192.168.1.10
```

## Test Result Sheet (fill in for your report)

| Test | Pass / Fail | Notes |
|---|---|---|
| 1 PC0 -> PC1 | | |
| 2 PC0 -> Router0 | | |
| 3 PC0 -> Router1 | | |
| 4 PC0 -> Server0 | | |
| 5 PC1 -> Server0 | | |
| 6 Server0 -> PC0 | | |
