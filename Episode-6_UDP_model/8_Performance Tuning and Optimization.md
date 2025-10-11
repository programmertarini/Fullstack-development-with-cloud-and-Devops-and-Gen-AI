Performance Tuning and Optimization
1. Socket Buffer Sizing
javascriptconst dgram = require('dgram');
const socket = dgram.createSocket('udp4');

// Increase receive buffer to handle burst traffic
socket.bind(3000, () => {
  socket.setRecvBufferSize(8 * 1024 * 1024); // 8 MB
  socket.setSendBufferSize(8 * 1024 * 1024); // 8 MB
  
  console.log('Recv buffer:', socket.getRecvBufferSize());
  console.log('Send buffer:', socket.getSendBufferSize());
});

// Why increase buffers?
// - High-frequency data (e.g., 1000 packets/second)
// - Prevent packet drops during processing spikes
// - Better handling of network congestion
2. Batch Processing
javascript// Instead of processing each packet individually (slow)
socket.on('message', (msg) => {
  processPacket(msg); // Process immediately
});

// Batch process for better performance (fast)
const messageQueue = [];
socket.on('message', (msg) => {
  messageQueue.push(msg);
});

setInterval(() => {
  if (messageQueue.length > 0) {
    processBatch(messageQueue.splice(0)); // Process all at once
  }
}, 10); // Every 10ms

function processBatch(messages) {
  // Batch processing is more efficient
  // - Reduce function call overhead
  // - Better CPU cache utilization
  // - Can optimize database writes (batch insert)
  messages.forEach(msg => {
    // Process each message
  });
}
3. Zero-Copy Operations
javascript// Avoid unnecessary buffer copies
const dgram = require('dgram');
const socket = dgram.createSocket('udp4');

// Bad: Multiple copies
socket.on('message', (msg) => {
  const str = msg.toString(); // Copy 1: Buffer to String
  const obj = JSON.parse(str); // Copy 2: String to Object
  processData(obj);
});

// Good: Minimize copies
socket.on('message', (msg) => {
  // Parse directly from buffer when possible
  const data = fastJsonParse(msg); // Single operation
  processData(data);
});

// Even better: Use binary formats
const msgpack = require('msgpack');
socket.on('message', (msg) => {
  const data = msgpack.decode(msg); // Efficient binary parsing
  processData(data);
});
4. Rate Limiting and Throttling
javascriptclass RateLimitedUDPServer {
  constructor(maxPacketsPerSecond) {
    this.socket = dgram.createSocket('udp4');
    this.maxPPS = maxPacketsPerSecond;
    this.packetCounts = new Map(); // IP -> count
    this.resetInterval = 1000; // Reset every second
    
    // Reset counters every second
    setInterval(() => {
      this.packetCounts.clear();
    }, this.resetInterval);
  }
  
  start(port) {
    this.socket.bind(port);
    
    this.socket.on('message', (msg, rinfo) => {
      const clientIP = rinfo.address;
      
      // Check rate limit
      const count = this.packetCounts.get(clientIP) || 0;
      if (count >= this.maxPPS) {
        console.log(`Rate limit exceeded for ${clientIP}`);
        return; // Drop packet
      }
      
      // Update counter
      this.packetCounts.set(clientIP, count + 1);
      
      // Process packet
      this.processMessage(msg, rinfo);
    });
  }
  
  processMessage(msg, rinfo) {
    // Handle message
  }
}

// Protect against UDP flood attacks
const server = new RateLimitedUDPServer(100); // Max 100 packets/second per IP
server.start(3000);
