## 1. Introduction

**TCP (Transmission Control Protocol)** is a connection-oriented, reliable, byte-stream transport layer protocol, originally defined in RFC 793 and updated by RFC 9293. It operates at Layer 4 (Transport) of both the OSI model and the TCP/IP model, sitting above IP and below application protocols such as HTTP, FTP, SSH, SMTP, and TLS.

Unlike UDP, TCP guarantees that data arrives complete, in order, and without duplication, at the cost of additional overhead and latency.

## 2. Key Characteristics

- **Connection-oriented** - a session is established via a three-way handshake before any data is exchanged.
- **Reliable delivery** - every byte is acknowledged; lost segments are detected and retransmitted automatically.
- **Ordered delivery** - sequence numbers let the receiver reorder segments that arrive out of order.
- **Flow control** - a sliding window mechanism stops a fast sender from overwhelming a slow receiver.
- **Congestion control** - algorithms such as slow start and congestion avoidance protect the network from overload.
- **Full-duplex** - data can flow in both directions at the same time over a single connection.
- **Byte-stream oriented** - TCP has no concept of "messages"; it moves a continuous stream of bytes, and framing is left to the application layer.

## 3. TCP Segment Header Format

A TCP segment header is normally 20 bytes (up to 60 with options).

| Field | Size | Description |
|---|---|---|
| Source Port | 16 bits | Sending application's port |
| Destination Port | 16 bits | Receiving application's port |
| Sequence Number | 32 bits | Position of first data byte in this segment |
| Acknowledgment Number | 32 bits | Next byte the sender expects to receive |
| Data Offset | 4 bits | Header length in 32-bit words |
| Reserved | 6 bits | Reserved for future use, set to 0 |
| Flags | 6-9 bits | URG, ACK, PSH, RST, SYN, FIN (plus ECE, CWR, NS) |
| Window Size | 16 bits | Number of bytes the sender is willing to receive (flow control) |
| Checksum | 16 bits | Error-checking for header and data |
| Urgent Pointer | 16 bits | Used with URG flag to mark urgent data |
| Options | variable | MSS, Window Scale, SACK, Timestamps, etc. |

### Control Flags

| Flag | Meaning |
|---|---|
| SYN | Synchronize sequence numbers; used to open a connection |
| ACK | Acknowledgment field is valid |
| FIN | Sender has finished sending data; used to close a connection |
| RST | Reset the connection abruptly |
| PSH | Push buffered data to the application immediately |
| URG | Urgent pointer field is significant |

## 4. Connection Establishment - The Three-Way Handshake

1. **SYN** - Client sends a segment with the SYN flag set and an Initial Sequence Number (ISN), e.g. seq = x.
2. **SYN-ACK** - Server replies with SYN and ACK flags set, its own ISN (seq = y), and acknowledges the client's ISN (ack = x + 1).
3. **ACK** - Client sends an ACK acknowledging the server's ISN (ack = y + 1). The connection is now ESTABLISHED.

This process synchronizes sequence numbers on both sides so each end knows where the other's byte stream begins.

## 5. Connection Termination - The Four-Way Handshake

1. Initiator sends **FIN** (it has no more data to send).
2. Receiver replies with **ACK**.
3. Receiver, once ready, sends its own **FIN**.
4. Initiator replies with **ACK** and enters the **TIME_WAIT** state for a period (typically 2 x Maximum Segment Lifetime) before fully closing, to guarantee any delayed duplicate segments are absorbed.

A connection can also be closed abruptly with an **RST** segment, which skips the graceful teardown.

## 6. TCP State Machine

| State | Description |
|---|---|
| CLOSED | No connection exists |
| LISTEN | Server waiting for an incoming connection request |
| SYN_SENT | Client has sent SYN, waiting for SYN-ACK |
| SYN_RECEIVED | Server has received SYN and replied with SYN-ACK |
| ESTABLISHED | Connection open; data can be exchanged |
| FIN_WAIT_1 | Local end has sent FIN |
| FIN_WAIT_2 | Local end's FIN was acknowledged, waiting for remote FIN |
| CLOSE_WAIT | Remote end sent FIN; local end has not yet closed |
| CLOSING | Both ends sent FIN simultaneously |
| LAST_ACK | Waiting for final ACK after sending FIN in response to remote FIN |
| TIME_WAIT | Waiting to ensure remote received the final ACK |

## 7. Flow Control - Sliding Window

TCP uses a **sliding window** protocol so a receiver can control how much unacknowledged data a sender may have in flight:

- The receiver advertises a **Window Size** in every segment - the number of bytes it can currently buffer.
- The sender may transmit up to that many bytes before it must stop and wait for an ACK.
- As the receiver's application reads data out of its buffer, the window "slides" forward and the receiver advertises a larger window.
- If the receiver's buffer fills up, it advertises a **zero window**, pausing the sender until space frees up (the sender periodically probes with a small segment to check).
- **Window Scaling** (an option negotiated at handshake) extends the 16-bit window field to support very large windows needed for high-bandwidth, high-latency links.

## 8. Congestion Control

Congestion control protects the *network* (not just the receiver) from being overwhelmed. Key algorithms (from RFC 5681 and later refinements):

- **Slow Start** - the sender begins with a small congestion window (cwnd), typically a few segments, and doubles it every round-trip time (exponential growth) until it reaches the slow-start threshold (ssthresh) or packet loss occurs.
- **Congestion Avoidance** - once cwnd passes ssthresh, growth becomes linear (roughly +1 segment per RTT) to probe capacity more cautiously.
- **Fast Retransmit** - if the sender receives three duplicate ACKs, it assumes a segment was lost and retransmits immediately, without waiting for the retransmission timer to expire.
- **Fast Recovery** - after a fast retransmit, ssthresh is halved and cwnd is set near the new ssthresh instead of collapsing all the way back to slow start.
- Modern variants include **CUBIC** (Linux default), **Reno**, **BBR** (Google, model-based rather than loss-based), and others, each tuning how cwnd responds to loss, delay, or explicit congestion signals (ECN).

## 9. Reliability Mechanisms

- **Sequence & Acknowledgment Numbers** - every byte sent is numbered; ACKs confirm cumulative receipt.
- **Retransmission Timeout (RTO)** - dynamically calculated from measured round-trip time (RTT); if an ACK isn't received in time, the segment is resent.
- **Selective Acknowledgment (SACK)** - lets the receiver tell the sender exactly which non-contiguous blocks of data arrived, avoiding unnecessary retransmission of already-received data.
- **Checksum** - detects corruption in the header and payload; corrupted segments are discarded and effectively treated as lost.
- **Duplicate detection** - sequence numbers let the receiver discard duplicate segments caused by retransmission.

## 10. TCP vs UDP

| Aspect | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (handshake required) | Connectionless |
| Reliability | Reliable - guarantees delivery and order | Best-effort - no delivery guarantee |
| Speed | Slower due to overhead | Faster, minimal overhead |
| Header size | 20-60 bytes | 8 bytes |
| Flow/Congestion control | Yes | No |
| Ordering | Guaranteed | Not guaranteed |
| Use cases | HTTP/HTTPS, FTP, SSH, SMTP, database connections | DNS, DHCP, VoIP, video streaming, online gaming |

## 11. Common TCP Ports

| Port | Service |
|---|---|
| 20/21 | FTP (data/control) |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS (also uses UDP) |
| 80 | HTTP |
| 110 | POP3 |
| 143 | IMAP |
| 443 | HTTPS |
| 3306 | MySQL |
| 3389 | RDP |

## 12. Security Considerations

- **SYN Flood** - an attacker sends many SYN segments without completing the handshake, exhausting the server's connection queue (mitigated with SYN cookies).
- **Session Hijacking** - an attacker guesses or intercepts sequence numbers to inject data into an established session.
- **RST Injection** - forged RST segments can be used to tear down legitimate connections.
- **Predictable ISNs** - weak Initial Sequence Number generation makes spoofing and hijacking easier; modern stacks randomize ISNs per RFC 6528.
- **Port scanning** - TCP's flag-based handshake behavior (e.g. SYN scans, FIN scans) is commonly used for reconnaissance, which is why IDS/IPS systems monitor for unusual flag combinations.

## 13. Summary

TCP trades speed for reliability. Its three-way handshake establishes synchronized sequence numbers, its sliding window provides flow control, and algorithms like slow start and congestion avoidance keep it from overwhelming the network. Sequence numbers, ACKs, checksums, and retransmission timers combine to guarantee that a byte stream arrives complete and in order - making TCP the foundation for most reliable internet services, from web browsing to email to remote administration.

---
*Related: [[OSI_MODEL]]*
