Overview of TCP (Transmission Control Protocol) and IP (Internet Protocol)
Internet Protocol (IP)
IP is the addressing and routing mechanism of the internet. Its primary job is to deliver packets from a source host to a destination host based on IP addresses contained in the packet headers.
Key Characteristics of IP

Connectionless Protocol - IP doesn't establish a connection before sending data; it simply forwards packets toward their destination
Best-Effort Delivery - IP makes no guarantees about packet delivery, order, or integrity - it just tries its best
Stateless - Each packet is treated independently; IP doesn't maintain information about previous packets
Unreliable by Design - This isn't a flaw, it's intentional simplicity that allows IP to be fast and scalable

IP Versions
IPv4 - Uses 32-bit addresses (e.g., 192.168.1.1), providing approximately 4.3 billion unique addresses
IPv6 - Uses 128-bit addresses (e.g., 2001:0db8:85a3:0000:0000:8a2e:0370:7334), providing virtually unlimited addresses
Transmission Control Protocol (TCP)
TCP sits on top of IP and adds the reliability, ordering, and error-checking that IP lacks. It creates a virtual connection between two hosts before data transmission begins.
Key Characteristics of TCP

Connection-Oriented - Establishes a reliable connection before data transfer through a three-way handshake
Reliable Delivery - Guarantees that data arrives intact and in the correct order
Stream-Based - Treats data as a continuous stream of bytes rather than discrete packets
Flow Control - Prevents overwhelming the receiver with too much data at once
Congestion Control - Adjusts transmission rate based on network conditions to prevent network congestion

The Three-Way Handshake
Before any data transmission, TCP establishes a connection:

SYN - Client sends a synchronization packet to the server
SYN-ACK - Server acknowledges and sends its own synchronization packet
ACK - Client acknowledges the server's response

This handshake ensures both sides are ready to communicate and agree on initial sequence numbers for tracking packets.
The Four-Way Connection Termination
When closing a connection:

FIN - One side sends a finish packet
ACK - Other side acknowledges
FIN - Other side sends its own finish packet
ACK - First side acknowledges

This ensures both sides have finished sending data before the connection closes completely.
