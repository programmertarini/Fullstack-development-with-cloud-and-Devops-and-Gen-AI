What is DoH:
DNS over HTTPS (DoH) is an internet security protocol that communicates domain name server information in an encrypted way over HTTPS connections. DoH uses port 443, just like HTTPS does. It blends in DNS requests in HTTPS queries, so DNS traffic gets unobserved among other HTTPS activity

What is DoT:
In DNS security, DoT stands for DNS over TLS, a standard protocol that encrypts Domain Name System (DNS) queries and responses using the Transport Layer Security (TLS) encryption. This process, often using TCP port 853, adds a layer of security to DNS traffic that was traditionally unencrypted, preventing eavesdropping, manipulation, and man-in-the-middle attacks by encrypting the connection between the DNS client and server

What is DNScript:
DNSCrypt is an open specification and protocol for authenticating and encrypting DNS traffic between a DNS client and a DNS resolver, thereby enhancing security by protecting against eavesdropping, DNS spoofing, and forgery. It wraps unmodified DNS queries in a cryptographic container and uses port 443 to mask traffic, though it has not been standardized by the IETF | Internet Engineering Task Force. Key features include increased privacy, integrity, and confidentiality of DNS communications. 

What is DNSSEC:
DNSSEC (Domain Name System Security Extensions) secures the internet's naming system by using digital signatures and public-key cryptography to verify the authenticity and integrity of DNS data. It protects against attacks like DNS spoofing and cache poisoning, which can redirect users to fake websites, ensuring that the DNS responses received are from authoritative sources and have not been tampered with during transit.  