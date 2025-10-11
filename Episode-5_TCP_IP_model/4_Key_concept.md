Key Concepts: IP Addressing
What is an IP Address
An IP address is a unique numerical identifier assigned to every device on a network. It serves two purposes: identifying the host and providing the location of the host in the network for routing purposes.
IPv4 Address Structure
Format - Four octets separated by dots (e.g., 192.168.1.100), where each octet is 8 bits representing values from 0 to 255
Network and Host Portions - Every IP address is divided into a network portion (identifies the network) and a host portion (identifies the specific device on that network)
Subnet Masks
A subnet mask determines which portion of an IP address represents the network and which represents the host.
CIDR Notation - 192.168.1.0/24 means the first 24 bits are the network portion, leaving 8 bits for hosts (256 addresses)
Why Subnets Matter - They allow network segmentation, improved security, and efficient IP address allocation. Understanding subnets helps you configure VPCs in cloud environments
Public vs Private IP Addresses
Private IP Ranges - Reserved for internal networks and not routable on the public internet:

10.0.0.0 to 10.255.255.255 (10.0.0.0/8)
172.16.0.0 to 172.31.255.255 (172.16.0.0/12)
192.168.0.0 to 192.168.255.255 (192.168.0.0/16)

Public IP Addresses - Globally unique and routable on the internet, assigned by Internet Service Providers
Network Address Translation (NAT) - Allows multiple devices with private IPs to share a single public IP address when accessing the internet. Your home router performs NAT, and cloud providers use it extensively
Special IP Addresses
127.0.0.1 (localhost) - Loopback address that always refers to the local machine, used for testing network applications without network hardware
0.0.0.0 - Represents "all IP addresses on the local machine" when used as a server binding address, or "no particular address" in routing contexts
255.255.255.255 - Broadcast address for sending data to all devices on the local network
IPv6 Addressing
Why IPv6 Exists - IPv4's 4.3 billion addresses are exhausted. IPv6 provides 340 undecillion addresses
Format - Eight groups of four hexadecimal digits (e.g., 2001:0db8:85a3:0000:0000:8a2e:0370:7334)
Address Compression - Consecutive zeros can be replaced with :: (used only once per address)
Link-Local Addresses - Always start with fe80:: and are automatically configured for local network communication
Developer Implications - Ensure your applications support IPv6, use libraries that handle both IPv4 and IPv6, and test on dual-stack environments

Key Concepts: Port Numbers
What are Port Numbers
Ports are 16-bit numbers (0-65535) that allow multiple network services to run on a single IP address simultaneously. They act as virtual endpoints for network connections, enabling a single server to host web, email, database, and other services concurrently.
The Socket Concept
A socket is the combination of an IP address and a port number (e.g., 192.168.1.100:8080). It's the complete addressing scheme that identifies a specific process on a specific machine.
Connection Identification - TCP connections are uniquely identified by a four-tuple: source IP, source port, destination IP, destination port
Port Number Ranges
Well-Known Ports (0-1023) - Reserved for system services and common protocols:

Port 80: HTTP
Port 443: HTTPS
Port 22: SSH
Port 21: FTP
Port 25: SMTP
Port 3306: MySQL
Port 5432: PostgreSQL

Registered Ports (1024-49151) - Assigned to user processes and applications:

Port 3000: Node.js development default
Port 8080: Alternative HTTP, often used for development
Port 5000: Flask development default
Port 27017: MongoDB

Dynamic/Private Ports (49152-65535) - Temporarily assigned by the operating system for client-side connections (ephemeral ports)
How Port Assignment Works
When your browser connects to a web server:

Your browser gets assigned a random ephemeral port (e.g., 52341) by your OS
It connects from 192.168.1.100:52341 to webserver.com:443
The server knows to send responses back to your specific port
Multiple browser tabs can connect simultaneously, each using a different ephemeral port

Port Binding and Listening
Server-Side - Applications must bind to a specific port to listen for incoming connections. Only one process can bind to a specific port at a time (except with SO_REUSEADDR socket option)
Privileged Ports - Ports below 1024 require root/administrator privileges to bind on Unix-like systems. This is why development servers often use ports like 3000, 8000, or 8080
0.0.0.0 vs 127.0.0.1 Binding - Binding to 0.0.0.0 allows connections from any network interface, while 127.0.0.1 only accepts local connections
Practical Considerations for Developers
Port Conflicts - If you get "address already in use" errors, another process is using that port. Use lsof -i :PORT (Unix) or netstat -ano | findstr :PORT (Windows) to find it
Firewalls and Security Groups - Cloud providers and corporate networks restrict port access. Your application might work locally but fail in production if ports aren't opened in security groups
Port Forwarding - NAT requires port forwarding configuration to expose services behind a router to the internet
Load Balancers - In production, load balancers listen on standard ports (80/443) and forward to applications on different ports across multiple servers
Docker Port Mapping - Container ports must be mapped to host ports (e.g., -p 8080:80 maps container's port 80 to host's 8080)

Key Concepts: Datagram Transmission
What is a Datagram
A datagram is a self-contained, independent packet of data that carries sufficient information to be routed from source to destination without relying on prior exchanges between the source and destination.
Connection-Oriented vs Connectionless
TCP (Connection-Oriented) - Establishes a connection, maintains state, guarantees delivery and order. Data is treated as a continuous stream
UDP (Connectionless with Datagrams) - No connection establishment, each datagram is independent, no guarantees of delivery or order
How IP Datagrams Work
Every IP packet is a datagram containing:
Header - Source IP, destination IP, protocol type, time-to-live (TTL), checksum, and other control information
Payload - The actual data being transmitted, which could be a TCP segment, UDP datagram, or other protocol data
Independent Routing - Each datagram may take a different path through the network. Routers make forwarding decisions independently for each packet based on current network conditions
Datagram Size and Fragmentation
Maximum Transmission Unit (MTU) - The largest packet size a network link can carry, typically 1500 bytes for Ethernet
IP Fragmentation - When a datagram is larger than the MTU, it gets split into smaller fragments. Each fragment has its own IP header with fragmentation information
Reassembly - Fragments are reassembled at the destination. If any fragment is lost, the entire datagram must be retransmitted
Path MTU Discovery - Modern systems determine the smallest MTU along the path and avoid fragmentation by sending smaller datagrams
UDP Datagrams
UDP sends discrete datagrams rather than establishing connections. Each UDP datagram is completely independent.
When to Use UDP
Real-Time Applications - Video streaming, VoIP, online gaming where speed matters more than perfect reliability. A dropped frame is better than a delayed one
DNS Queries - Quick, single-request-response transactions where retrying is simpler than maintaining a connection
Broadcast/Multicast - Sending data to multiple recipients simultaneously
IoT and Sensor Data - When devices send frequent updates and occasional data loss is acceptable
UDP Characteristics
No Handshake - Data transmission begins immediately without connection setup overhead
No Acknowledgments - Sender doesn't know if data arrived unless the application layer implements its own confirmation
No Ordering - Datagrams may arrive out of order; application must handle sequencing if needed
No Congestion Control - UDP doesn't slow down in response to network congestion, which can be both an advantage and a problem
Lower Overhead - UDP header is only 8 bytes compared to TCP's 20+ bytes, reducing bandwidth usage
Datagram Delivery Challenges
Packet Loss - Network congestion, router buffer overflow, or transmission errors can cause datagrams to be dropped
Out-of-Order Delivery - Different routes through the network mean later datagrams might arrive before earlier ones
Duplication - Network issues can sometimes cause the same datagram to arrive multiple times
Why This Matters - When building applications, you must choose the appropriate protocol. RESTful APIs, web pages, and file transfers need TCP's reliability. Live video, gaming, and real-time metrics often work better with UDP's low latency
Application-Layer Protocols Built on Datagrams
QUIC - Modern protocol used by HTTP/3 that provides TCP-like reliability on top of UDP, offering better performance in poor network conditions
WebRTC - Real-time communication for video/audio that uses UDP datagrams with optional reliability mechanisms
DNS - Uses UDP for queries (with fallback to TCP for large responses)
