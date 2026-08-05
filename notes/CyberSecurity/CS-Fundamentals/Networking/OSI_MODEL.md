# OSI Model (Open Systems Interconnection Model)

**Difficulty:** ⭐ Beginner\
**Prerequisites:** None\
**Estimated Reading Time:** 25 minutes\
**Last Updated:** 2026-08-05\
**Category:** Networking\
**Tags:** `#OSI` `#Networking` `#Cybersecurity` `#CCNA`
`#EthicalHacking`

------------------------------------------------------------------------

# Table of Contents

1. Introduction
2. What is the OSI Model?
3. Definition
4. History
5. Why was the OSI Model Created?
6. Advantages of the OSI Model
7. Real-Life Example
8. The Seven Layers
9. Mnemonics
10. Data Flow
11. Encapsulation
12. De-encapsulation
13. Layer 7 -- Application Layer
14. Layer 6 -- Presentation Layer
15. Layer 5 -- Session Layer
16. Layer 4 -- Transport Layer
17. Layer 3 -- Network Layer
18. Layer 2 -- Data Link Layer
19. Layer 1 -- Physical Layer
20. Protocol Data Units (PDU)
21. Addressing Used
22. Devices at Each Layer
23. Encapsulation Process
24. De-encapsulation Process
25. Layer Responsibilities Summary
26. Interview Questions
27. Quick Revision
28. Key Points

------------------------------------------------------------------------

# Introduction

The **OSI (Open Systems Interconnection) Model** is a **7-layer
reference model** developed by **ISO** to explain how data travels from
one computer to another through a network.

It is a **reference model**, **not a protocol**.

The OSI model helps: - Understand networking - Troubleshoot network
problems - Design interoperable networks - Learn cybersecurity and
ethical hacking

------------------------------------------------------------------------

# Why was the OSI Model Created?

Before the OSI Model:

- Different vendors used different networking methods.
- Devices from different companies often could not communicate.
- Troubleshooting was difficult.

The OSI Model created a common standard for network communication.

------------------------------------------------------------------------

# Advantages

- Standard communication
- Easy troubleshooting
- Modular architecture
- Vendor independence
- Better security planning
- Easier protocol development

------------------------------------------------------------------------

# Real-Life Example

Ordering food online:

  Real Life         OSI Layer
  ----------------- --------------
  Place order       Application
  Translate order   Presentation
  Confirm order     Session
  Delivery          Transport
  Choose route      Network
  Travel on roads   Data Link
  Road              Physical

------------------------------------------------------------------------

# Seven Layers

    Layer Name
  ------- --------------
        7 Application
        6 Presentation
        5 Session
        4 Transport
        3 Network
        2 Data Link
        1 Physical

------------------------------------------------------------------------

# Mnemonics

Top → Bottom

**All People Seem To Need Data Processing**

Bottom → Top

**Please Do Not Throw Sausage Pizza Away**

------------------------------------------------------------------------

# Data Flow

Sending:

Application → Presentation → Session → Transport → Network → Data Link →
Physical

Receiving:

Physical → Data Link → Network → Transport → Session → Presentation →
Application

------------------------------------------------------------------------

# Encapsulation

Each layer adds its own header before passing data to the next layer.

Application Data → Transport Header → IP Header → MAC Header + Trailer →
Bits

------------------------------------------------------------------------

# De-encapsulation

The receiver removes the headers layer by layer until the original data
is obtained.

------------------------------------------------------------------------

# Layer 7 -- Application

Purpose: - Provides network services directly to users.

Protocols: - HTTP - HTTPS - FTP - SMTP - POP3 - IMAP - DNS - DHCP -
SSH - Telnet

Examples: - Chrome - Firefox - Outlook - Gmail

PDU: **Data**

------------------------------------------------------------------------

# Layer 6 -- Presentation

Responsibilities: - Translation - Encryption - Decryption -
Compression - Decompression

Formats: - JPEG - PNG - GIF - MP3 - MPEG - ASCII - Unicode

PDU: **Data**

------------------------------------------------------------------------

# Layer 5 -- Session

Responsibilities: - Create session - Maintain session - Terminate
session - Authentication - Authorization

Protocols: - NetBIOS - RPC - PPTP

PDU: **Data**

------------------------------------------------------------------------

# Layer 4 -- Transport

Responsibilities: - Segmentation - Reassembly - Reliable delivery - Flow
control - Error recovery

Protocols: - TCP - UDP

TCP: - Reliable - Connection-oriented - Ordered delivery

UDP: - Faster - Connectionless - No guarantee of delivery

Common Ports:

  Port    Service
  ------- -------------
  20/21   FTP
  22      SSH
  23      Telnet
  25      SMTP
  53      DNS
  67      DHCP
  68      DHCP Client
  80      HTTP
  110     POP3
  143     IMAP
  443     HTTPS

PDU: - TCP → Segment - UDP → Datagram

------------------------------------------------------------------------

# Layer 3 -- Network

Responsibilities: - Routing - Logical addressing - Path selection

Protocols: - IPv4 - IPv6 - ICMP - OSPF - RIP - BGP - ARP (commonly
discussed here)

Device: - Router - Layer 3 Switch

Address: - IP Address

PDU: - Packet

------------------------------------------------------------------------

# Layer 2 -- Data Link

Responsibilities: - Framing - MAC Addressing - Error Detection - Flow
Control

Protocols: - Ethernet - PPP - HDLC

Devices: - Switch - Bridge - NIC - Wireless Access Point

Address: - MAC Address

PDU: - Frame

------------------------------------------------------------------------

# Layer 1 -- Physical

Responsibilities: - Transmit bits - Electrical signals - Optical
signals - Radio signals

Devices: - Hub - Repeater - Cables - Connectors

PDU: - Bits

------------------------------------------------------------------------

# Protocol Data Units

  Layer          PDU
  -------------- --------------------
  Application    Data
  Presentation   Data
  Session        Data
  Transport      Segment / Datagram
  Network        Packet
  Data Link      Frame
  Physical       Bits

------------------------------------------------------------------------

# Addressing Used

  Layer       Address
  ----------- -------------
  Transport   Port Number
  Network     IP Address
  Data Link   MAC Address

------------------------------------------------------------------------

# Devices at Each Layer

  Layer   Devices
  ------- --------------------------
  7       Applications
  6       SSL/TLS Libraries
  5       Session Services
  4       Firewall / Load Balancer
  3       Router
  2       Switch
  1       Hub / Repeater

------------------------------------------------------------------------

# Layer Responsibilities Summary

  Layer   Responsibility
  ------- --------------------------
  7       User Services
  6       Encryption & Compression
  5       Session Management
  4       Reliable Delivery
  3       Routing
  2       Framing & MAC
  1       Signal Transmission

------------------------------------------------------------------------

# Interview Questions

1. How many layers are in the OSI Model? → **7**
2. Which layer performs routing? → **Network**
3. Which layer uses IP addresses? → **Layer 3**
4. Which layer uses MAC addresses? → **Layer 2**
5. Which layer performs encryption? → **Presentation**
6. Which layer provides reliable communication? → **Transport**
7. Which device works at Layer 3? → **Router**
8. Which protocol is reliable? → **TCP**

------------------------------------------------------------------------

# Quick Revision

  Layer   Name           PDU       Address   Device
  ------- -------------- --------- --------- ------------------
  7       Application    Data      \-        Applications
  6       Presentation   Data      \-        SSL/TLS
  5       Session        Data      \-        Session Services
  4       Transport      Segment   Port      Firewall
  3       Network        Packet    IP        Router
  2       Data Link      Frame     MAC       Switch
  1       Physical       Bits      \-        Hub

------------------------------------------------------------------------

# Key Points

- OSI has **7 layers**.
- It is a **reference model**.
- Sending: Layer 7 → Layer 1.
- Receiving: Layer 1 → Layer 7.
- Layer 3 uses **IP addresses**.
- Layer 2 uses **MAC addresses**.
- Layer 4 uses **Port Numbers**.
- TCP is reliable; UDP is faster.
