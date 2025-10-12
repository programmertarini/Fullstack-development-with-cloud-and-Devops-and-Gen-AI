## Purpose of the Handshake: Establishing a Reliable Connection

### Primary Objectives

#### 1. **Mutual Agreement**
Both client and server agree to establish a connection. This prevents data from being sent to an unwilling or unavailable recipient.

#### 2. **Sequence Number Synchronization**
- Each side informs the other of its starting sequence number
- Enables proper ordering of data segments
- Allows detection of duplicate or missing segments
- Prevents old duplicate segments from interfering with new connections

#### 3. **Connection Parameters Exchange**
Both parties negotiate and agree on:
- **Maximum Segment Size (MSS)**: Largest amount of data in a single segment
- **Window Size**: Amount of data receiver can buffer
- **Window Scaling**: For high-bandwidth networks
- **Selective Acknowledgment (SACK)**: Enhanced error recovery
- **Timestamps**: For RTT measurement and PAWS protection

#### 4. **Resource Allocation**
- Server allocates receive and send buffers
- Connection state structures are created
- Ports are bound and reserved for this connection

#### 5. **Full-Duplex Communication Setup**
The handshake establishes bidirectional communication channels, allowing data to flow in both directions simultaneously.

### Security Considerations

#### SYN Flood Attack Protection
- **Problem**: Attackers send many SYN packets without completing handshake
- **Impact**: Server resources exhausted (memory, connection table)
- **Mitigations**:
  - SYN Cookies: Encode connection state in sequence number
  - Connection rate limiting
  - SYN proxy/firewall filtering
  - Reducing SYN-RECEIVED timeout

#### Random ISN Generation
- Prevents connection hijacking attacks
- Makes it harder to inject malicious packets
- RFC 6528 recommends cryptographically secure random ISN

---