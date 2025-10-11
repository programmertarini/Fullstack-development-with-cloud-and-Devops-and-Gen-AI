1. Datagram-Based Transmission
What is a Datagram?
A datagram is a self-contained, independent packet of data that includes:

Source and destination addresses (IP + port)
The data payload
No connection state or sequence information

Key Characteristics:

Independent: Each datagram is treated separately
Self-sufficient: Contains all addressing information
No relationship: Datagrams don't know about each other

Developer Perspective
javascript// Node.js UDP datagram example
const dgram = require('dgram');
const socket = dgram.createSocket('udp4');

// Each send() creates an independent datagram
socket.send('Message 1', 3000, 'localhost'); // Datagram 1
socket.send('Message 2', 3000, 'localhost'); // Datagram 2
socket.send('Message 3', 3000, 'localhost'); // Datagram 3

// These three datagrams:
// - May arrive out of order (3, 1, 2)
// - May take different network paths
// - Are completely independent
// - Don't affect each other if one is lost
Real-Life Analogy
Sending three postcards to the same address:

Each postcard is independent
They might take different routes
They might arrive on different days or out of order
If one gets lost, others still arrive
Each postcard has complete address information

Datagram Size Limitations
javascript// Maximum safe UDP datagram size
const MAX_SAFE_UDP_SIZE = 508; // bytes (to avoid fragmentation)
const PRACTICAL_LIMIT = 1472;   // bytes (Ethernet MTU - IP - UDP headers)
const THEORETICAL_MAX = 65507;  // bytes (IP packet size - headers)

// Example: Splitting large data
function sendLargeData(data, socket, port, host) {
  const chunks = [];
  for (let i = 0; i < data.length; i += 1400) {
    chunks.push(data.slice(i, i + 1400));
  }
  
  chunks.forEach((chunk, index) => {
    const packet = {
      id: generateId(),
      index: index,
      total: chunks.length,
      data: chunk
    };
    socket.send(JSON.stringify(packet), port, host);
  });
}

2. Low Overhead
What Does "Low Overhead" Mean?
Overhead refers to the extra data and processing required beyond the actual payload you want to send.
TCP Overhead Includes:

Connection establishment (SYN, SYN-ACK, ACK packets)
Acknowledgment packets for received data
Sequence numbers and acknowledgment numbers (8 bytes)
Window size management (2 bytes)
Options field (variable, up to 40 bytes)
Checksum calculations (2 bytes)
Retransmission timers and buffers
State management (connection tracking)

UDP Overhead Includes:

Only 8-byte header (source port, destination port, length, checksum)
Basic checksum calculation (optional in IPv4)
That's it!

Performance Impact
Bandwidth Efficiency Example:
Sending 100 bytes of actual data:

TCP:
- Minimum header: 20 bytes
- ACK packet: 40 bytes (20 IP + 20 TCP)
- Total overhead: 60 bytes (60% overhead)
- If retransmission needed: 120 bytes overhead

UDP:
- Header: 8 bytes
- Total overhead: 8 bytes (8% overhead)
- No retransmission overhead
CPU and Memory Impact
TCP Connection State:
javascript// TCP maintains extensive state per connection
class TCPConnection {
  constructor() {
    this.state = 'CLOSED'; // LISTEN, SYN_SENT, ESTABLISHED, etc.
    this.sendSequence = 0;
    this.receiveSequence = 0;
    this.sendWindow = 65535;
    this.receiveWindow = 65535;
    this.sendBuffer = new Buffer(65535);
    this.receiveBuffer = new Buffer(65535);
    this.retransmissionQueue = [];
    this.timers = {
      retransmission: null,
      persist: null,
      keepalive: null,
      timeWait: null
    };
    // ... more state
  }
}
UDP "Connection" State:
javascript// UDP maintains minimal state
class UDPSocket {
  constructor() {
    this.socket = createSocket();
    this.port = null;
    // That's basically it!
  }
}
Real-World Scenarios
Scenario 1: Multiplayer Game Server (10,000 concurrent players)
Using TCP:

Memory: ~1 GB (connection state for 10,000 connections)
CPU: ~40% (managing acknowledgments, retransmissions)
Bandwidth: ~200 Mbps (including overhead)

Using UDP:

Memory: ~100 MB (minimal state)
CPU: ~10% (just sending/receiving)
Bandwidth: ~120 Mbps (minimal overhead)

Scenario 2: IoT Sensor Network (1000 sensors)
Each sensor sends 20 bytes of data every 10 seconds:
TCP per sensor:

Handshake: 3 packets every connection
Data + ACK: 2 packets per transmission
Connection management: Ongoing overhead
Battery drain: High (constant connection state)

UDP per sensor:

Data: 1 packet per transmission
No connection: No handshake needed
Battery drain: Low (send and sleep)

javascript// IoT sensor with UDP (efficient)
function sendSensorReading(temperature) {
  const payload = Buffer.from([temperature, batteryLevel, sensorId]);
  socket.send(payload, SERVER_PORT, SERVER_IP);
  // Done! Go back to sleep
}

// IoT sensor with TCP (inefficient)
function sendSensorReading(temperature) {
  client.connect(SERVER_PORT, SERVER_IP, () => {
    // Handshake overhead
    client.write(Buffer.from([temperature, batteryLevel, sensorId]));
    client.on('data', () => {
      // Wait for ACK
      client.end();
      // Teardown overhead
    });
  });
}

3. Connectionless Communication
What Does "Connectionless" Mean?
Connectionless: No dedicated end-to-end connection is established. Each datagram is treated independently without any relationship to other datagrams.
Connection-oriented (TCP): A virtual circuit is established before data transfer, maintained during communication, and terminated afterward.
Connection Lifecycle Comparison
TCP Connection Lifecycle:
Client                                Server
  |                                      |
  |----------- SYN ------------------>  |  (1. Request connection)
  |                                      |
  |<--------- SYN-ACK ----------------|  |  (2. Acknowledge + Request)
  |                                      |
  |----------- ACK ------------------>  |  (3. Acknowledge)
  |                                      |
  |========== DATA ==================>  |  (4. Data transfer)
  |<========= DATA ===================  |
  |========== DATA ==================>  |
  |                                      |
  |----------- FIN ------------------>  |  (5. Close connection)
  |<---------- ACK -------------------|  |
  |<---------- FIN -------------------|  |
  |----------- ACK ------------------>  |

Time spent: ~150-300ms (handshake + teardown)
UDP "Connection" Lifecycle:
Client                                Server
  |                                      |
  |========== DATA ==================>  |  (Just send!)
  |========== DATA ==================>  |
  |========== DATA ==================>  |

Time spent: ~0ms (no setup or teardown)
Developer Implementation
TCP Server (Connection-oriented):
javascriptconst net = require('net');

const server = net.createServer((socket) => {
  // Each client gets a dedicated socket connection
  console.log('Client connected:', socket.remoteAddress);
  
  // Connection state is maintained
  let clientData = {
    connected: true,
    receivedBytes: 0,
    sentBytes: 0
  };
  
  socket.on('data', (data) => {
    clientData.receivedBytes += data.length;
    // Data arrives in order, reliably
    console.log('Received:', data.toString());
  });
  
  socket.on('end', () => {
    console.log('Client disconnected');
    clientData.connected = false;
  });
});

server.listen(3000);
UDP Server (Connectionless):
javascriptconst dgram = require('dgram');

const server = dgram.createSocket('udp4');

server.on('message', (msg, rinfo) => {
  // No connection concept - just receive datagrams
  // Each message is independent
  // We don't know if this is first, last, or only message from this client
  console.log(`Received from ${rinfo.address}:${rinfo.port} - ${msg.toString()}`);
  
  // To reply, we just send back to the address
  server.send('Response', rinfo.port, rinfo.address);
});

server.bind(3000);
Advantages of Connectionless
1. Faster Communication
javascript// UDP: Immediate send
const startTime = Date.now();
socket.send(data, port, host);
console.log(`Sent in ${Date.now() - startTime}ms`); // ~0ms

// TCP: Wait for connection
const startTime = Date.now();
const client = net.connect(port, host, () => {
  client.write(data);
  console.log(`Connected and sent in ${Date.now() - startTime}ms`); // ~50-150ms
});
2. Better for Many Clients
javascript// UDP: Single socket can handle unlimited clients
const dgram = require('dgram');
const server = dgram.createSocket('udp4');

server.on('message', (msg, rinfo) => {
  // Can handle messages from 1 million different clients
  // No connection state to maintain
  handleMessage(msg, rinfo.address, rinfo.port);
});

// TCP: Each client needs a connection
const net = require('net');
const server = net.createServer((socket) => {
  // Each connection consumes memory and file descriptors
  // Limited by OS resources (usually 65535 max connections)
});
3. Efficient Broadcasting
javascript// UDP: Broadcast to multiple recipients
const message = Buffer.from('Server announcement');
socket.setBroadcast(true);
socket.send(message, 0, message.length, 8888, '255.255.255.255');
// One packet reaches all devices on network

// TCP: Must connect to each recipient individually
clients.forEach(client => {
  client.write('Server announcement');
  // Separate packet and connection for each client
});
Disadvantages of Connectionless
1. No Reliability
javascript// UDP: Fire and forget
socket.send(criticalData, port, host);
// Was it received? No idea!
// Application must implement acknowledgments if needed

// TCP: Guaranteed delivery
client.write(criticalData);
// TCP ensures it arrives or notifies you of failure
2. No Flow Control
javascript// UDP: Can overwhelm receiver
for (let i = 0; i < 10000; i++) {
  socket.send(data, port, host);
  // Sending too fast? Receiver's buffer might overflow!
}

// TCP: Automatic flow control
for (let i = 0; i < 10000; i++) {
  client.write(data);
  // TCP automatically slows down if receiver is overwhelmed
}
3. Application Complexity
javascript// UDP: Must implement reliability if needed
class ReliableUDP {
  constructor() {
    this.pendingAcks = new Map();
    this.sequenceNumber = 0;
  }
  
  sendReliable(data, port, host) {
    const packet = {
      seq: this.sequenceNumber++,
      data: data,
      timestamp: Date.now()
    };
    
    this.socket.send(JSON.stringify(packet), port, host);
    
    // Wait for ACK
    this.pendingAcks.set(packet.seq, {
      packet: packet,
      retries: 0,
      timer: setTimeout(() => this.retransmit(packet.seq), 1000)
    });
  }
  
  retransmit(seq) {
    // Implement retransmission logic
    // Handle timeouts
    // Manage acknowledgments
    // ... lots of complexity!
  }
}

// TCP: Built-in reliability
client.write(data); // Done!
