DNS Hierarchy {#dns-hierarchy}
🌳 The DNS Tree Structure
The DNS system is organized like an upside-down tree, with the root at the top and specific domains as leaves.
Understanding the Hierarchy Levels
Level 1: Root Level (".")
The Foundation of DNS:

13 root server clusters worldwide
Named a.root-servers.net through m.root-servers.net
They know where to find all TLD servers
Managed by different organizations globally

Why 13 Root Servers?

Originally limited by UDP packet size (512 bytes)
Each uses anycast technology (multiple physical servers with same IP)
Distributed globally for redundancy and speed

What Root Servers Know:

Where to find .com servers
Where to find .org servers
Where to find country code domains (.uk, .ca, .de)
Nothing about specific websites

Level 2: Top-Level Domains (TLDs)
Generic TLDs (gTLDs):

.com - Commercial (but anyone can register)
.org - Organizations
.net - Network infrastructure (originally)
.edu - Educational institutions
.gov - US Government
.mil - US Military

Country Code TLDs (ccTLDs):

.uk - United Kingdom
.ca - Canada
.de - Germany
.jp - Japan
.au - Australia

New gTLDs (Introduced 2012+):

.tech, .app, .dev, .blog, .shop
More descriptive and brand-specific options

Level 3: Second-Level Domains (SLD)
What You Actually Register:

google.com, github.com, stackoverflow.com
You have full control over all subdomains
This is what you pay domain registrars for

Level 4+: Subdomains
Infinite Flexibility:

www.example.com
api.example.com
cdn.assets.example.com
user-profile.app.mobile.example.com

🌍 Global DNS Infrastructure
Anycast Technology Explained
Think of anycast like having multiple McDonald's restaurants with the same phone number:

You call the McDonald's number
The call automatically goes to the nearest location
If that location is closed, it goes to the next nearest

In DNS:

Multiple servers share the same IP address
Traffic automatically routes to the closest server
If one server fails, traffic goes to the next closest
Users always get the fastest response

Root Server Distribution
The 13 root servers are actually hundreds of physical servers:

Each letter (a-m) represents a cluster
Clusters are distributed globally
Your query goes to the nearest cluster
Provides redundancy and speed

🔄 How Hierarchy Enables Scalability
Delegation Principle:

Root servers only know about TLDs
TLD servers only know about second-level domains
Each level delegates responsibility downward
No single server needs to know everything

Benefits:

Scales to billions of domain names
Changes can be made independently at each level
Failures are isolated to specific branches
Administration is distributed globally