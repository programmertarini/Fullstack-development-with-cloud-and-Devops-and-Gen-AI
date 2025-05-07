## Understanding IP Addresses, Domain Names, and Routing

### IP Addresses
- **Definition**: Numerical labels assigned to each device on a network that uses the Internet Protocol for communication.
- **IPv4 Structure**: 32-bit numbers expressed as four octets (e.g., 192.168.1.1), allowing approximately 4.3 billion unique addresses.
- **IPv6 Structure**: 128-bit addresses (e.g., 2001:0db8:85a3:0000:0000:8a2e:0370:7334) created to address IPv4 address exhaustion.
- **Public vs. Private IP**: Public IPs are globally unique and routable; private IPs (like 192.168.x.x) are used within local networks.
- **Static vs. Dynamic IP**: Static remains constant; dynamic changes periodically as assigned by DHCP servers.
- **NAT (Network Address Translation)**: Allows multiple devices on a local network to share a single public IP address.
- **Subnetting**: Dividing IP networks into sub-networks to improve security and performance.
- **IP Geolocation**: Approximate physical location determination based on IP address databases.

### Domain Names
- **Purpose**: Human-readable addresses that map to numerical IP addresses.
- **Domain Name Structure**: Hierarchical system consisting of top-level domains (.com, .org), second-level domains (google.com), and subdomains (mail.google.com).
- **Domain Registration Process**: Involves registering through accredited registrars who maintain the reservation in a central database.
- **ICANN**: The Internet Corporation for Assigned Names and Numbers oversees domain name system management.
- **IDNs (Internationalized Domain Names)**: Allow domain names in non-Latin characters.
- **DNS Records Types**: Include A (address), MX (mail exchange), CNAME (canonical name), TXT (text), and others.
- **TTL (Time to Live)**: Specifies how long DNS records should be cached before requiring revalidation.
- **Domain Name Lifecycle**: Registration, renewal, expiration, redemption, and deletion phases.

### Routing
- **Definition**: The process of selecting paths for traffic across one or more networks.
- **Routing Tables**: Databases maintained by routers containing information about network destinations.
- **Routing Protocols**:
  - **BGP (Border Gateway Protocol)**: Used between autonomous systems that make up the Internet
  - **OSPF (Open Shortest Path First)**: Used within larger enterprise networks
  - **RIP (Routing Information Protocol)**: Simpler protocol used in smaller networks
- **Autonomous Systems (AS)**: Collections of connected IP routing prefixes controlled by network operators.
- **Routing Metrics**: Criteria used to determine optimal paths (hop count, bandwidth, delay, etc.).
- **Path Determination**: Algorithms that calculate the most efficient route based on current network conditions.
- **Route Advertisements**: How routers share information about which networks they can reach.
- **Anycast Routing**: Multiple servers share the same IP address, with traffic routed to the nearest one.
- **Traffic Engineering**: Techniques to optimize network performance by managing data flow patterns.

