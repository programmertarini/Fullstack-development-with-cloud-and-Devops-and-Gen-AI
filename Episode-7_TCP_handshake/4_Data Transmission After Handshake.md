## Data Transmission After Handshake

### Immediate Post-Handshake State

Once the 3-way handshake completes:
1. Connection enters **ESTABLISHED** state on both ends
2. Both parties have synchronized sequence numbers
3. Buffers and resources are allocated
4. Application layer can begin sending data

### How Data Flows

#### Sending Data
```
Client                                Server
  |                                     |
  |--- Data (seq=x+1, len=100) ------->|
  |                                     |
  |<------ ACK (ack=x+101) ------------|
  |                                     |
  |--- Data (seq=x+101, len=200) ----->|
  |                                     |
  |<------ ACK (ack=x+301) ------------|
```

#### Key Mechanisms

**1. Segmentation**
- Application data is broken into TCP segments
- Each segment has a sequence number
- Segment size limited by MSS (typically 1460 bytes for Ethernet)

**2. Acknowledgments**
- Receiver sends ACK for received data
- ACK number indicates next expected byte
- Can acknowledge multiple segments at once (cumulative ACK)

**3. Flow Control**
- Receiver advertises **window size** in each segment
- Sender cannot send more than receiver's window allows
- Prevents overwhelming the receiver (sliding window protocol)

**4. Congestion Control**
- Sender maintains **congestion window (cwnd)**
- Starts small and grows (slow start, congestion avoidance)
- Reduces on packet loss
- Algorithms: Reno, Cubic, BBR, etc.

**5. Retransmission**
- If ACK not received within timeout, segment is retransmitted
- Timeout calculated based on RTT (Round-Trip Time)
- Duplicate ACKs trigger fast retransmit

### Bidirectional Data Flow

Both client and server can send data simultaneously:

```
Client                                Server
  |                                     |
  |--- Data (seq=x+1, ack=y+1) ------->|
  |                                     |
  |<-- Data (seq=y+1, ack=x+101) ------|
  |                                     |
  |--- Data (seq=x+101, ack=y+201) --->|
  |                                     |
  |<-- ACK (ack=x+301) -----------------|
```

### Connection Termination

When data transfer is complete, connection is closed using a **4-way handshake**:

```
1. FIN: Client sends FIN (wants to close)
2. ACK: Server acknowledges FIN
3. FIN: Server sends FIN (agrees to close)
4. ACK: Client acknowledges FIN
```

---
