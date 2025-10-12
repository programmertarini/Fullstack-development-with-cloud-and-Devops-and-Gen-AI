## Key Concepts

### 1. Reliable Connection Establishment

#### What Makes TCP Reliable?

**Connection Establishment Reliability**
- SYN and SYN-ACK are retransmitted if lost
- Exponential backoff for retransmissions
- Connection attempt timeout after multiple failures

**Handshake Failure Scenarios**
- **SYN lost**: Client retransmits SYN after timeout (typically 3 seconds)
- **SYN-ACK lost**: Server retransmits SYN-ACK, client eventually retransmits SYN
- **Final ACK lost**: Server retransmits SYN-ACK, client resends ACK
- **Connection refused**: Server sends RST (reset) if port is not listening

**State Machine**
```
Client States:            Server States:
CLOSED                    CLOSED
  ↓ (send SYN)              ↓ (listen)
SYN-SENT                  LISTEN
  ↓ (receive SYN-ACK)       ↓ (receive SYN)
  ↓ (send ACK)            SYN-RECEIVED
ESTABLISHED                 ↓ (receive ACK)
                          ESTABLISHED
```

### 2. Sequence Numbers

#### Purpose and Characteristics

**Why Sequence Numbers?**
- **Ordering**: Reassemble segments in correct order
- **Deduplication**: Detect and discard duplicate segments
- **Loss Detection**: Identify missing segments
- **Connection Uniqueness**: Prevent confusion between connections

**Sequence Number Mechanics**
- **Initial Sequence Number (ISN)**: Randomly generated 32-bit number
- **Byte-level Numbering**: Each byte of data has a sequence number
- **Wraparound**: Sequence numbers wrap around after 2^32 (4.3 billion)
- **SYN Consumes**: SYN flag consumes one sequence number

**Example Data Transmission**
```
Client sends:
- SYN with seq=1000
- Server responds with SYN-ACK, ack=1001
- Client sends data: seq=1001, length=500 bytes
- Next segment: seq=1501 (1001 + 500)
```

**Relative vs Absolute Sequence Numbers**
- **Absolute**: Actual ISN values (large random numbers)
- **Relative**: Normalized to start at 0 (used in tools like Wireshark)
- Relative makes packet analysis easier for humans

### 3. Acknowledgements

#### How ACKs Work

**ACK Mechanism**
- **Cumulative ACK**: ACK number indicates next expected byte
- **Example**: ACK=5001 means "I've received all bytes up to 5000"
- **Piggybacking**: ACK can be sent with data in opposite direction

**Types of Acknowledgements**

**1. Cumulative Acknowledgement**
```
Received: seq=1000-1499, 1500-1999, 2000-2499
Sends: ACK=2500 (acknowledges all three)
```

**2. Selective Acknowledgement (SACK)**
```
Received: seq=1000-1499, 2000-2499 (1500-1999 missing)
Sends: ACK=1500, SACK=2000-2500
Allows sender to retransmit only missing segment
```

**3. Duplicate ACK**
```
Received: seq=1000-1499, 2000-2499, 2500-2999
Missing: 1500-1999
Sends: ACK=1500, ACK=1500, ACK=1500 (duplicates)
Trigger: 3 duplicate ACKs initiate fast retransmit
```

#### Delayed ACKs
- TCP doesn't ACK every segment immediately
- Wait up to 200ms to piggyback ACK with data
- Or ACK every other segment
- Reduces network overhead

#### ACK Numbers in Handshake
```
Step 1: Client sends SYN, seq=1000
Step 2: Server sends SYN-ACK, seq=5000, ack=1001
        (acknowledges client's SYN)
Step 3: Client sends ACK, seq=1001, ack=5001
        (acknowledges server's SYN)
```

---