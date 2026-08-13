# OSI Model & TCP/IP Fundamentals

**Label:** `Learning / Overview`

## What It Is
The layered model (OSI's 7 layers, or the more practically-used 4-layer TCP/IP model) describing how data moves from an application on one host to an application on another: Physical/Data Link → Network (IP, routing) → Transport (TCP/UDP) → Application (HTTP, DNS, etc.).

## Why Architects Use It
A shared mental model for isolating *which layer* a problem lives at — "can't connect" could mean anything from a cable fault to a DNS misconfiguration to an application-layer authentication failure, and the layer model is the fastest way to narrow that down systematically.

## Key Concepts for Interviews
- **TCP vs UDP**: TCP is connection-oriented, reliable, ordered (three-way handshake, retransmission) — right for most application traffic; UDP is connectionless, no delivery guarantee, lower overhead — right for latency-sensitive/loss-tolerant traffic (DNS queries, streaming, some real-time protocols).
- **Three-way handshake**: SYN → SYN-ACK → ACK establishes a TCP connection; understanding this matters for diagnosing connection timeouts vs. connection resets (RST) — a timeout suggests packets aren't arriving at all (firewall/routing), a reset suggests they arrived but were actively rejected.
- **MTU/fragmentation**: mismatched MTU across a path (common in VPN/tunnel scenarios) causes packet fragmentation or silent drops for oversized packets — a classic, hard-to-diagnose "works for small requests, fails for large ones" symptom.
- **Ports and sockets**: a connection is uniquely identified by the 4-tuple (source IP, source port, destination IP, destination port) — relevant when reasoning about NAT and load-balancer behavior.

## Common Mistakes (in Interview Answers)
- Conflating "network" issues with "application" issues without isolating the layer first.
- Not mentioning the layer model explicitly when a troubleshooting question is asked, missing an easy signal of structured thinking.

## Principal-Level Questions
- How would you determine whether a "connection refused" vs. a "connection timeout" error points to different root causes?
- Explain what happens at the packet level when a client behind an Azure Load Balancer accesses a backend VM.

## Scenario Question
> Large file uploads intermittently fail over a site-to-site VPN, while small requests work fine.

## Strong Answer (Outline)
1. This symptom pattern (works for small payloads, fails for large ones) is a classic MTU/fragmentation signature.
2. Check the VPN tunnel's effective MTU versus the path's actual MTU — tunnel encapsulation overhead (IPsec headers) reduces the usable payload size, and if the OS/application doesn't correctly negotiate/handle this via Path MTU Discovery or TCP MSS clamping, oversized packets get silently dropped by a device that can't fragment them (especially if the "Don't Fragment" bit is set, common in modern stacks).
3. Fix: configure MSS clamping on the VPN gateway/router or adjust MTU settings so packets fit within the tunnel's actual capacity.
4. Validate with a packet capture showing retransmissions/drops correlating with payload size crossing the effective MTU threshold.

## Follow-Up Questions
- How would you diagnose this without direct packet-capture access on the client side?
- How does this differ diagnostically for UDP-based traffic instead of TCP?

## Trade-offs
N/A — this is foundational knowledge; the "trade-off" framing applies more to the higher-level design files in this repository.
