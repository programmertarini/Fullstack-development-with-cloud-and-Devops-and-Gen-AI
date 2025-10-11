UDP (User Datagram Protocol) - Comprehensive Developer Notes
What is UDP and How Does It Differ from TCP?
UDP Overview
UDP is a connectionless, lightweight transport layer protocol that sends data packets (called datagrams) without establishing a connection or guaranteeing delivery. Think of it like sending postcards through the mail - you write the address and drop it off, but you don't get confirmation that it arrived.
Core Differences Between UDP and TCP
TCP (Transmission Control Protocol):

Connection-oriented: Requires a three-way handshake before data transfer (SYN, SYN-ACK, ACK)
Reliable: Guarantees data delivery, order, and integrity
Flow control: Manages data transmission rate to prevent overwhelming receiver
Error checking: Detects and retransmits lost packets automatically
Overhead: Higher due to acknowledgments, sequencing, and control mechanisms
Slower: Due to handshake, acknowledgments, and retransmissions
Analogy: Like a phone call - connection established, conversation flows both ways, confirmation of receipt

UDP (User Datagram Protocol):

Connectionless: No handshake required, just send data immediately
Unreliable: No guarantee of delivery, order, or arrival
No flow control: Sends data at whatever rate application chooses
Minimal error checking: Basic checksum only (optional in IPv4)
Low overhead: Minimal header (8 bytes vs TCP's 20+ bytes)
Faster: No connection setup or acknowledgment delays
Analogy: Like sending postcards - write address, send, hope it arrives

UDP Header Structure (Developer Perspective)
0      7 8     15 16    23 24    31
+--------+--------+--------+--------+
|   Source Port   | Destination Port|
+--------+--------+--------+--------+
|     Length      |    Checksum     |
+--------+--------+--------+--------+
|          Data (Payload)           |
+-----------------------------------+
Header Fields (Total: 8 bytes)

Source Port (2 bytes): Sender's port number
Destination Port (2 bytes): Receiver's port number
Length (2 bytes): Total datagram length (header + data)
Checksum (2 bytes): Optional error checking (required in IPv6)