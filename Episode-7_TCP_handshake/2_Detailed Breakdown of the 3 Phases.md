## Detailed Breakdown of the 3 Phases

### Phase 1: SYN (Synchronize)

**Initiator**: Client  
**Direction**: Client → Server

#### What Happens
1. Client decides to establish a connection with the server
2. Client generates a random **Initial Sequence Number (ISN)** (e.g., seq = x)
3. Client sends a TCP segment with:
   - **SYN flag** set to 1
   - **Sequence number** = x (randomly generated)
   - Other TCP options (window size, MSS, etc.)

#### Technical Details
```
TCP Header Fields:
- Source Port: Client's port number
- Destination Port: Server's port (e.g., 80 for HTTP, 443 for HTTPS)
- Sequence Number: x (random ISN)
- Acknowledgment Number: 0
- Flags: SYN = 1
- Window Size: Client's receive buffer size
- Options: MSS, Window Scaling, Timestamps, etc.
```

#### Purpose
- Inform the server that the client wants to establish a connection
- Communicate the client's initial sequence number
- Share TCP parameters (Maximum Segment Size, window size, etc.)

#### Client State
- Transitions from **CLOSED** to **SYN-SENT** state
- Starts a retransmission timer in case the SYN is lost

---

### Phase 2: SYN-ACK (Synchronize-Acknowledge)

**Initiator**: Server  
**Direction**: Server → Client

#### What Happens
1. Server receives the SYN packet
2. If the server accepts the connection:
   - Generates its own random **Initial Sequence Number (ISN)** (e.g., seq = y)
   - Sends a TCP segment with:
     - **SYN flag** set to 1 (server's synchronization)
     - **ACK flag** set to 1 (acknowledging client's SYN)
     - **Sequence number** = y (server's random ISN)
     - **Acknowledgment number** = x + 1 (client's ISN + 1)

#### Technical Details
```
TCP Header Fields:
- Source Port: Server's port
- Destination Port: Client's port
- Sequence Number: y (server's random ISN)
- Acknowledgment Number: x + 1 (acknowledging client's SYN)
- Flags: SYN = 1, ACK = 1
- Window Size: Server's receive buffer size
- Options: Server's TCP parameters
```

#### Purpose
- Acknowledge receipt of the client's SYN
- Inform the client of the server's initial sequence number
- Indicate the server is ready to establish the connection
- Communicate server's TCP parameters

#### Server State
- Transitions from **LISTEN** to **SYN-RECEIVED** state
- Allocates resources for the connection
- Starts a retransmission timer

---

### Phase 3: ACK (Acknowledge)

**Initiator**: Client  
**Direction**: Client → Server

#### What Happens
1. Client receives the SYN-ACK packet
2. Client sends a TCP segment with:
   - **ACK flag** set to 1
   - **Sequence number** = x + 1 (incremented from initial)
   - **Acknowledgment number** = y + 1 (server's ISN + 1)
3. Connection is now **ESTABLISHED**

#### Technical Details
```
TCP Header Fields:
- Source Port: Client's port
- Destination Port: Server's port
- Sequence Number: x + 1
- Acknowledgment Number: y + 1 (acknowledging server's SYN)
- Flags: ACK = 1
- Window Size: Client's receive buffer size
- Data: May optionally carry application data (TCP Fast Open)
```

#### Purpose
- Acknowledge receipt of the server's SYN-ACK
- Confirm the connection establishment
- Signal both sides are ready for data transmission

#### Connection State
- **Client**: Transitions from **SYN-SENT** to **ESTABLISHED**
- **Server**: Transitions from **SYN-RECEIVED** to **ESTABLISHED** when it receives this ACK
- Resources are fully allocated on both sides
- Data transmission can now begin

---
