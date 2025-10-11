Common Developer Mistakes and Best Practices
Mistake 1: Using UDP for Critical Data Without Reliability Layer
Wrong:
javascript// Sending financial transaction via UDP - BAD!
function processPayment(amount, userId) {
  const transaction = { amount, userId, timestamp: Date.now() };
  socket.send(JSON.stringify(transaction), port, host);
  // What if this packet is lost? Money could disappear!
}
Right:
javascript// Use TCP for financial transactions - GOOD!
const https = require('https');
function processPayment(amount, userId) {
  https.post('https://api.payment.com/transaction', {
    body: JSON.stringify({ amount, userId })
  });
  // TCP ensures reliable delivery
}
Mistake 2: Not Handling Packet Reordering in UDP
Wrong:
javascript// Assuming UDP packets arrive in order - BAD!
let messageBuffer = '';

socket.on('message', (msg) => {
  messageBuffer += msg.toString();
  // Packets might arrive: 3, 1, 2 - buffer gets corrupted!
});
Right:
javascript// Implementing sequence numbers - GOOD!
const receivedPackets = new Map();
let expectedSequence = 0;

socket.on('message', (msg) => {
  const packet = JSON.parse(msg.toString());
  receivedPackets.set(packet.sequence, packet.data);
  
  // Process in order
  while (receivedPackets.has(expectedSequence)) {
    processData(receivedPackets.get(expectedSequence));
    receivedPackets.delete(expectedSequence);
    expectedSequence++;
  }
});
Mistake 3: Sending Too Much Data in Single UDP Packet
Wrong:
javascript// Sending large file via single UDP packet - BAD!
const fileData = fs.readFileSync('large-file.zip'); // 10 MB
socket.send(fileData, port, host);
// This will fragment and likely fail!
Right:
javascript// Chunking data appropriately - GOOD!
const CHUNK_SIZE = 1400; // Safe size for UDP
const fileData = fs.readFileSync('video.mp4');

for (let i = 0; i < fileData.length; i += CHUNK_SIZE) {
  const chunk = fileData.slice(i, i + CHUNK_SIZE);
  const packet = {
    sequence: Math.floor(i / CHUNK_SIZE),
    data: chunk.toString('base64'),
    total: Math.ceil(fileData.length / CHUNK_SIZE)
  };
  socket.send(JSON.stringify(packet), port, host);
}
Best Practice 1: Implement Application-Level Acknowledgments for Critical UDP Data
javascriptclass ReliableUDPTransfer {
  constructor(socket) {
    this.socket = socket;
    this.pendingAcks = new Map();
    this.timeout = 3000; // 3 seconds
  }
  
  sendWithAck(data, port, host) {
    const messageId = this.generateId();
    const packet = {
      id: messageId,
      data: data,
      timestamp: Date.now()
    };
    
    return new Promise((resolve, reject) => {
      this.socket.send(JSON.stringify(packet), port, host);
      
      const timer = setTimeout(() => {
        this.pendingAcks.delete(messageId);
        reject(new Error('Timeout waiting for ACK'));
      }, this.timeout);
      
      this.pendingAcks.set(messageId, { resolve, reject, timer });
    });
  }
  
  handleAck(ackPacket) {
    const pending = this.pendingAcks.get(ackPacket.id);
    if (pending) {
      clearTimeout(pending.timer);
      pending.resolve();
      this.pendingAcks.delete(ackPacket.id);
    }
  }
}
Best Practice 2: Use UDP for Telemetry and Metrics
javascript// Perfect use case for UDP: Application metrics
class MetricsReporter {
  constructor(host, port) {
    this.socket = dgram.createSocket('udp4');
    this.host = host;
    this.port = port;
  }
  
  reportMetric(metricName, value) {
    const metric = `${metricName}:${value}|g`;
    this.socket.send(metric, this.port, this.host);
    // Don't care if one metric is lost - next one comes in 1 second
  }
  
  startReporting() {
    setInterval(() => {
      this.reportMetric('cpu_usage', process.cpuUsage());
      this.reportMetric('memory_usage', process.memoryUsage().heapUsed);
      this.reportMetric('active_connections', this.getConnectionCount());
      // UDP is perfect here - fast, low overhead, occasional loss acceptable
    }, 1000);
  }
}
Best Practice 3: Implement Exponential Backoff for UDP Retries
javascriptclass UDPClientWithBackoff {
  async sendWithRetry(data, port, host, maxRetries = 5) {
    let attempt = 0;
    let delay = 100; // Start with 100ms
    
    while (attempt < maxRetries) {
      try {
        await this.sendAndWaitForAck(data, port, host, delay);
        return; // Success!
      } catch (error) {
        attempt++;
        if (attempt >= maxRetries) {
          throw new Error('Max retries exceeded');
        }
        
        // Exponential backoff: 100ms, 200ms, 400ms, 800ms, 1600ms
        await this.sleep(delay);
        delay *= 2;
      }
    }
  }
  
  sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}