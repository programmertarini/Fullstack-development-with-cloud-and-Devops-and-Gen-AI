## Key Concepts in Detail

### Internet Service Providers (ISPs)
- **Definition**: Companies that provide Internet access to end-users and organizations.
- **Tier Structure**:
  - **Tier 1**: Global backbone providers that exchange traffic freely with other Tier 1 networks
  - **Tier 2**: Regional providers that purchase transit from Tier 1 providers
  - **Tier 3**: Local ISPs serving end customers
- **Last Mile Connection**: The final leg connecting the ISP's network to the customer's premises.
- **Access Technologies**:
  - **DSL**: Uses existing telephone lines
  - **Cable**: Uses cable television infrastructure
  - **Fiber Optic**: Uses light pulses through glass fibers
  - **Satellite**: Wireless connection from orbital satellites
  - **Cellular/Mobile**: Uses cellular network infrastructure
- **Bandwidth Caps and Data Throttling**: Limits imposed by ISPs on data consumption.
- **Network Neutrality**: Principle that ISPs should treat all Internet traffic equally, without discrimination.
- **Peering Agreements**: Arrangements between networks to exchange traffic directly.
- **Transit Agreements**: Paid arrangements where one network pays another for Internet access.
- **IXPs (Internet Exchange Points)**: Physical locations where different networks connect to exchange traffic directly.
- **ISP Redundancy**: Enterprise practices of using multiple ISPs for reliability.

### Routers
- **Definition**: Specialized networking devices that forward data packets between computer networks.
- **Core Functions**:
  - **Packet Forwarding**: Moving packets from input to appropriate output interfaces
  - **Routing Table Management**: Maintaining and updating information about network paths
  - **Network Address Translation**: Mapping private to public IP addresses
  - **Firewall Capabilities**: Filtering traffic based on security rules
- **Router Components**:
  - **CPU**: Processes routing protocols and maintains tables
  - **Memory**: Stores routing tables and configuration
  - **Interfaces**: Physical or virtual connections to different networks
  - **System Bus**: Connects all internal components
- **Router Types**:
  - **Edge Routers**: Connect to external networks
  - **Core Routers**: High-capacity devices in the network backbone
  - **Distribution Routers**: Aggregate connections from access routers
  - **Home/SOHO Routers**: Combined router/switch/access point devices for small networks
- **Routing Decisions**: Based on destination IP address in each packet and routing table entries.
- **Routing Protocols Classification**:
  - **Interior Gateway Protocols**: Used within autonomous systems
  - **Exterior Gateway Protocols**: Used between autonomous systems
- **Router Security Features**: Access control lists, stateful inspection, VPN termination.
- **QoS Implementation**: Traffic prioritization mechanisms.
- **Router Administration**: Command-line interfaces, web interfaces, or network management systems.

### DNS (Domain Name System)
- **Definition**: Hierarchical, distributed database that translates domain names to IP addresses.
- **DNS Resolution Process**:
  1. User enters domain name in browser
  2. Computer checks local DNS cache
  3. If not found, query sent to recursive DNS resolver (typically at ISP)
  4. Resolver queries root name servers
  5. Root servers direct to appropriate TLD (Top-Level Domain) servers
  6. TLD servers direct to authoritative name servers
  7. Authoritative servers provide the IP address
  8. Resolver returns IP to client, which then connects to the website
- **DNS Record Types**:
  - **A/AAAA**: Maps domain to IPv4/IPv6 address
  - **CNAME**: Alias from one domain to another
  - **MX**: Specifies mail servers
  - **TXT**: Text information (often used for verification)
  - **NS**: Specifies nameservers for the domain
  - **SOA**: Administrative information about the zone
- **DNS Hierarchy**:
  - **Root Zone**: Managed by ICANN, represented by a dot (.)
  - **TLDs**: Generic (.com, .org) or country-code (.uk, .jp)
  - **Second-Level Domains**: Registered by organizations (google.com)
  - **Subdomains**: Divisions within a domain (mail.google.com)
- **DNS Caching**: Temporary storage of results to improve performance.
- **DNSSEC (DNS Security Extensions)**: Authenticates DNS responses to prevent spoofing.
- **DNS over HTTPS/TLS**: Encrypts DNS queries for privacy.
- **Split-Horizon DNS**: Provides different answers based on the source of queries.
- **DNS Load Balancing**: Distributes traffic across multiple servers.
- **Dynamic DNS**: Allows automatic updating of IP addresses for domains.
- **DNS Propagation**: Time required for DNS changes to be visible across the Internet.