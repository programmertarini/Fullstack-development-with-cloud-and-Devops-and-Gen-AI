4. IP Addresses, Domain Names, and Routing - Comprehensive Theory
IP Address Theory
What is an IP Address?
An IP address is a unique numerical identifier for every device on the Internet. Think of it as a postal address for network communication.
IPv4 Structure (32-bit addresses):
192.168.1.100
└─ Dotted decimal notation
   
Binary representation:
11000000.10101000.00000001.01100100

Breakdown:
192 = Network portion
168 = Network portion  
1   = Subnet portion
100 = Host portion
Address Classes (Historical but important to understand):
javascript// Class A: 1.0.0.0 to 126.0.0.0
// - First bit: 0
// - 7 bits for network, 24 bits for hosts
// - 16,777,214 hosts per network
const classA = {
  range: '1.0.0.0 - 126.255.255.255',
  networks: 126,
  hostsPerNetwork: 16777214,
  usage: 'Large organizations, ISPs'
};

// Class B: 128.0.0.0 to 191.255.0.0  
// - First bits: 10
// - 14 bits for network, 16 bits for hosts
// - 65,534 hosts per network
const classB = {
  range: '128.0.0.0 - 191.255.255.255',
  networks: 16384,
  hostsPerNetwork: 65534,
  usage: 'Medium-sized organizations'
};

// Class C: 192.0.0.0 to 223.255.255.0
// - First bits: 110  
// - 21 bits for network, 8 bits for hosts
// - 254 hosts per network
const classC = {
  range: '192.0.0.0 - 223.255.255.255',
  networks: 2097152,
  hostsPerNetwork: 254,
  usage: 'Small organizations, home networks'
};
Modern CIDR Notation:
CIDR (Classless Inter-Domain Routing) replaced the class system:
javascript// CIDR notation examples:
const networks = {
  '192.168.1.0/24': {
    description: 'Network: 192.168.1.0, Subnet mask: 255.255.255.0',
    usableHosts: 254,
    range: '192.168.1.1 - 192.168.1.254'
  },
  
  '10.0.0.0/8': {
    description: 'Large private network',
    usableHosts: 16777214,
    range: '10.0.0.1 - 10.255.255.254'
  },
  
  '172.16.0.0/12': {
    description: 'Medium private network',  
    usableHosts: 1048574,
    range: '172.16.0.1 - 172.31.255.254'
  }
};

// Calculating network information:
function analyzeNetwork(cidr) {
  const [network, prefixLength] = cidr.split('/');
  const hostBits = 32 - parseInt(prefixLength);
  const totalAddresses = Math.pow(2, hostBits);
  const usableHosts = totalAddresses - 2; // Subtract network and broadcast
  
  return {
    network: network,
    prefixLength: prefixLength,
    hostBits: hostBits,
    totalAddresses: totalAddresses,
    usableHosts: usableHosts
  };
}
Special IP Address Ranges:
javascriptconst specialRanges = {
  // Private addresses (RFC 1918) - not routed on Internet
  private: [
    '10.0.0.0/8',        // 16.7M addresses
    '172.16.0.0/12',     // 1M addresses  
    '192.168.0.0/16'     // 65K addresses
  ],
  
  // Loopback - always refers to local machine
  loopback: '127.0.0.0/8',  // 127.0.0.1 most common
  
  // Link-local - automatic IP assignment
  linkLocal: '169.254.0.0/16',
  
  // Multicast - one-to-many communication
  multicast: '224.0.0.0/4',
  
  // Broadcast - rarely used
  broadcast: '255.255.255.255'
};

// In your development:
const developmentConfig = {
  local: 'http://127.0.0.1:3000',        // Loopback
  network: 'http://192.168.1.100:3000',  // Private network
  production: 'https://api.myapp.com'    // Public Internet
};
IPv6 Theory:
IPv6 addresses the IPv4 address exhaustion problem:
javascriptconst ipv6Features = {
  addressLength: '128 bits (vs IPv4 32 bits)',
  totalAddresses: '340,282,366,920,938,463,463,374,607,431,768,211,456',
  notation: 'Hexadecimal with colons',
  
  examples: {
    full: '2001:0db8:85a3:0000:0000:8a2e:0370:7334',
    compressed: '2001:db8:85a3::8a2e:370:7334',  // :: represents consecutive zeros
    loopback: '::1',  // IPv6 equivalent of 127.0.0.1
    linkLocal: 'fe80::/10'  // Automatic local addressing
  },
  
  advantages: [
    'Virtually unlimited addresses',
    'Built-in security (IPSec)',
    'Simplified routing',
    'No NAT needed',
    'Better mobile support'
  ]
};
Domain Name System (DNS) - Deep Theory
Why DNS Exists:
Humans prefer names (google.com) over numbers (172.217.14.110). DNS is the Internet's phone book.
Hierarchical Structure:
www.api.mycompany.com.
└── Root (.)
    └── Top-Level Domain (com)
        └── Second-Level Domain (mycompany)  
            └── Subdomain (api)
                └── Host (www)
DNS Resolution Process (Step by Step):
javascript// When you type "www.example.com" in browser:

const dnsResolutionSteps = [
  {
    step: 1,
    action: 'Check local DNS cache',
    location: 'Your computer',
    result: 'Cache miss - not found locally'
  },
  {
    step: 2, 
    action: 'Query recursive DNS resolver',
    location: 'Your ISP or 8.8.8.8',
    result: 'Resolver doesn\'t know, starts recursive query'
  },
  {
    step: 3,
    action: 'Query root name server',
    location: 'One of 13 root servers worldwide',
    result: 'Returns: "Ask .com name server at 192.5.6.30"'
  },
  {
    step: 4,
    action: 'Query TLD name server',
    location: '.com name server',  
    result: 'Returns: "Ask example.com name server at 93.184.216.34"'
  },
  {
    step: 5,
    action: 'Query authoritative name server',
    location: 'example.com name server',
    result: 'Returns: "www.example.com is 93.184.216.34"'
  },
  {
    step: 6,
    action: 'Return IP to browser',
    location: 'Your computer',
    result: 'Browser connects to 93.184.216.34'
  }
];

// This entire process typically takes 20-100 milliseconds
DNS Record Types (Essential for Developers):
javascriptconst dnsRecords = {
  A: {
    purpose: 'Maps domain to IPv4 address',
    example: 'example.com.    300    IN    A    93.184.216.34',
    usage: 'Primary website hosting'
  },
  
  AAAA: {
    purpose: 'Maps domain to IPv6 address', 
    example: 'example.com.    300    IN    AAAA    2606:2800:220:1:248:1893:25c8:1946',
    usage: 'IPv6 website hosting'
  },
  
  CNAME: {
    purpose: 'Maps domain to another domain name',
    example: 'www.example.com.    300    IN    CNAME    example.com.',
    usage: 'Aliases, CDN endpoints'
  },
  
  MX: {
    purpose: 'Mail server for domain',
    example: 'example.com.    300    IN    MX    10 mail.example.com.',
    usage: 'Email routing'
  },
  
  TXT: {
    purpose: 'Arbitrary text data',
    example: 'example.com.    300    IN    TXT    "v=spf1 include:_spf.google.com ~all"',
    usage: 'Email verification, domain verification, SPF records'
  },
  
  NS: {
    purpose: 'Name server for domain',
    example: 'example.com.    300    IN    NS    ns1.example.com.',
    usage: 'DNS delegation'
  }
};

// TTL (Time To Live) in seconds - how long to cache the record
DNS Caching Theory:
javascript// DNS caching happens at multiple levels:
const cachingLevels = {
  browser: {
    ttl: '1-30 minutes',
    purpose: 'Fastest access for repeated requests'
  },
  
  operatingSystem: {
    ttl: '1-24 hours', 
    purpose: 'Shared across all applications'
  },
  
  recursiveResolver: {
    ttl: 'Based on record TTL',
    purpose: 'Shared across all users of that resolver'
  },
  
  authoritativeServer: {
    ttl: 'Set by domain owner',
    purpose: 'Controls how long others cache the record'
  }
};

// Why caching matters for developers:
fetch('https://api.example.com/data')
// If DNS is cached: ~0ms DNS lookup
// If DNS not cached: ~20-100ms DNS lookup
// This affects your application's performance!
Routing Theory - How Packets Find Their Destination
Routing is Path Finding:
Routers are like GPS systems for network packets - they determine the best path from source to destination.
Routing Table Structure:
javascript// Simplified routing table:
const routingTable = [
  {
    destination: '0.0.0.0/0',         // Default route (catch-all)
    gateway: '192.168.1.1',           // Next hop router
    interface: 'eth0',                // Network interface to use
    metric: 100                       // Route priority (lower = better)
  },
  {
    destination: '192.168.1.0/24',    // Local network
    gateway: '0.0.0.0',               // Directly connected (no gateway)
    interface: 'eth0',
    metric: 1
  },
  {
    destination: '10.0.0.0/8',        // Specific network
    gateway: '192.168.1.254',
    interface: 'eth0', 
    metric: 50
  }
];

// Route selection algorithm:
function selectRoute(destinationIP, routingTable) {
  let bestMatch = null;
  let longestPrefix = 0;
  
  for (const route of routingTable) {
    if (isInNetwork(destinationIP, route.destination)) {
      const prefixLength = route.destination.split('/')[1];
      if (prefixLength > longestPrefix) {
        longestPrefix = prefixLength;
        bestMatch = route;
      }
    }
  }
  
  return bestMatch;
}
Routing Protocols Theory:
Interior Gateway Protocols (Within an organization):
javascriptconst igpProtocols = {
  RIP: {
    name: 'Routing Information Protocol',
    algorithm: 'Distance Vector',
    metric: 'Hop count',
    maxHops: 15,
    convergence: 'Slow',
    usage: 'Simple networks, rarely used today'
  },
  
  OSPF: {
    name: 'Open Shortest Path First', 
    algorithm: 'Link State',
    metric: 'Cost (bandwidth-based)',
    maxHops: 'Unlimited',
    convergence: 'Fast',
    usage: 'Enterprise networks, ISPs'
  },
  
  EIGRP: {
    name: 'Enhanced Interior Gateway Routing Protocol',
    algorithm: 'Advanced Distance Vector',
    metric: 'Composite (bandwidth, delay, reliability)',
    maxHops: 255,
    convergence: 'Very fast',
    usage: 'Cisco networks'
  }
};
Exterior Gateway Protocols (Between organizations):
javascriptconst egpProtocols = {
  BGP: {
    name: 'Border Gateway Protocol',
    purpose: 'Inter-domain routing',
    scope: 'Internet backbone',
    pathSelection: 'Policy-based (not just shortest path)',
    importance: 'THE protocol that makes the Internet work',
    
    attributes: [
      'AS_PATH - List of networks packet has traversed',
      'ORIGIN - How route was learned',  
      'NEXT_HOP - IP address of next router',
      'LOCAL_PREF - Local preference value',
      'MULTI_EXIT_DISC - Metric for multiple paths'
    ]
  }
};