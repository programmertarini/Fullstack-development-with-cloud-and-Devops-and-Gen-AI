Security Considerations
1. UDP Flood DDoS Attack
Problem: Attackers send massive amounts of UDP packets to overwhelm server.
Mitigation:
javascriptclass SecureUDPServer {
  constructor() {
    this.socket = dgram.createSocket('udp4');
    this.rateLimiter = new Map();
    this.blacklist = new Set();
  }
  
  start(port) {
    this.socket.bind(port);
    
    this.socket.on('message', (msg, rinfo) => {
      const clientIP = rinfo.address;
      
      // Check blacklist
      if (this.blacklist.has(clientIP)) {
        return; // Ignore blacklisted IPs
      }
      
      // Rate limiting
      if (this.isRateLimitExceeded(clientIP)) {
        console.log(`Potential attack from ${clientIP}`);
        this.blacklist.add(clientIP);
        
        // Auto-remove after 1 hour
        setTimeout(() => {
          this.blacklist.delete(clientIP);
        }, 3600000);
        
        return;
      }
      
      // Validate packet size
      if (msg.length > 1500) {
        console.log(`Suspicious large packet from ${clientIP}`);
        return;
      }
      
      // Process legitimate traffic
      this.processMessage(msg, rinfo);
    });
  }
  
  isRateLimitExceeded(clientIP) {
    const now = Date.now();
    const record = this.rateLimiter.get(clientIP) || { count: 0, timestamp: now };
    
    // Reset if more than 1 second passed
    if (now - record.timestamp > 1000) {
      record.count = 1;
      record.timestamp = now;
    } else {
      record.count++;
    }
    
    this.rateLimiter.set(clientIP, record);
    
    return record.count > 100; // Max 100 packets/second
  }
}
2. Packet Spoofing
Problem: Attackers can forge source IP addresses in UDP packets.
Mitigation:
javascript// Implement challenge-response to verify client
class AuthenticatedUDPServer {
  constructor() {
    this.socket = dgram.createSocket('udp4');
    this.authenticatedClients = new Map(); // IP -> token
    this.challenges = new Map(); // IP -> challenge
  }
  
  start(port) {
    this.socket.bind(port);
    
    this.socket.on('message', (msg, rinfo) => {
      const packet = JSON.parse(msg.toString());
      
      if (packet.type === 'auth_request') {
        this.handleAuthRequest(rinfo);
      } else if (packet.type === 'auth_response') {
        this.handleAuthResponse(packet, rinfo);
      } else if (packet.type === 'data') {
        this.handleData(packet, rinfo);
      }
    });
  }
  
  handleAuthRequest(rinfo) {
    // Generate random challenge
    const challenge = crypto.randomBytes(32).toString('hex');
    this.challenges.set(rinfo.address, challenge);
    
    // Send challenge
    const response = JSON.stringify({ type: 'challenge', challenge });
    this.socket.send(response, rinfo.port, rinfo.address);
  }
  
  handleAuthResponse(packet, rinfo) {
    const expectedChallenge = this.challenges.get(rinfo.address);
    
    // Verify response (in real app, use proper crypto)
    const expectedResponse = crypto
      .createHash('sha256')
      .update(expectedChallenge + SECRET_KEY)
      .digest('hex');
    
    if (packet.response === expectedResponse) {
      // Client authenticated
      const token = crypto.randomBytes(16).toString('hex');
      this.authenticatedClients.set(rinfo.address, token);
      
      this.socket.send(
        JSON.stringify({ type: 'auth_success', token }),
        rinfo.port,
        rinfo.address
      );
    }
  }
  
  handleData(packet, rinfo) {
    // Verify authentication token
    const expectedToken = this.authenticatedClients.get(rinfo.address);
    if (packet.token !== expectedToken) {
      console.log(`Unauthenticated packet from ${rinfo.address}`);
      return;
    }
    
    // Process authenticated data
    this.processData(packet.data);
  }
}
3. Amplification Attacks
Problem: Attacker uses UDP services to amplify DDoS attacks.
Example: DNS amplification, NTP amplification
Mitigation:
javascript// Prevent being used as amplification vector
class SafeUDPService {
  constructor() {
    this.socket = dgram.createSocket('udp4');
    this.requestLog = new Map(); // Track requests
  }
  
  start(port) {
    this.socket.bind(port);
    
    this.socket.on('message', (msg, rinfo) => {
      // Rule 1: Ensure response is not larger than request
      const responseSize = this.calculateResponseSize(msg);
      if (responseSize > msg.length * 2) {
        // Potential amplification - limit response
        console.log(`Limiting response to ${rinfo.address}`);
        return;
      }
      
      // Rule 2: Rate limit per IP
      if (this.isSuspiciousPattern(rinfo.address)) {
        console.log(`Blocking suspicious activity from ${rinfo.address}`);
        return;
      }
      
      // Process request
      this.handleRequest(msg, rinfo);
    });
  }
  
  isSuspiciousPattern(ip) {
    const now = Date.now();
    const requests = this.requestLog.get(ip) || [];
    
    // Keep only recent requests (last 10 seconds)
    const recentRequests = requests.filter(time => now - time < 10000);
    recentRequests.push(now);
    this.requestLog.set(ip, recentRequests);
    
    // More than 50 requests in 10 seconds is suspicious
    return recentRequests.length > 50;
  }
}

Debugging UDP Applications
1. Packet Loss Detection
javascriptclass UDPDebugger {
  constructor() {
    this.socket = dgram.createSocket('udp4');
    this.stats = {
      sent: 0,
      received: 0,
      lost: 0,
      outOfOrder: 0
    };
    this.lastSequence = -1;
  }
  
  sendWithTracking(data, port, host) {
    const packet = {
      sequence: this.stats.sent,
      data: data,
      timestamp: Date.now()
    };
    
    this.socket.send(JSON.stringify(packet), port, host);
    this.stats.sent++;
    
    console.log(`📤 Sent packet #${packet.sequence}`);
  }
  
  onReceive(msg) {
    const packet = JSON.parse(msg.toString());
    this.stats.received++;
    
    // Check for packet loss
    if (packet.sequence !== this.lastSequence + 1) {
      const lost = packet.sequence - this.lastSequence - 1;
      this.stats.lost += lost;
      console.log(`⚠️  Lost ${lost} packet(s)!`);
    }
    
    // Check for out-of-order delivery
    if (packet.sequence < this.lastSequence) {
      this.stats.outOfOrder++;
      console.log(`🔀 Out-of-order packet: #${packet.sequence}`);
    }
    
    this.lastSequence = Math.max(this.lastSequence, packet.sequence);
    
    console.log(`📥 Received packet #${packet.sequence}`);
    this.printStats();
  }
  
  printStats() {
    const lossRate = ((this.stats.lost / this.stats.sent) * 100).toFixed(2);
    console.log(`
📊 UDP Stats:
   Sent: ${this.stats.sent}
   Received: ${this.stats.received}
   Lost: ${this.stats.lost} (${lossRate}%)
   Out of Order: ${this.stats.outOfOrder}
    `);
  }
}
2. Network Latency Measurement
javascriptclass LatencyMeasurement {
  constructor() {
    this.socket = dgram.createSocket('udp4');
    this.pendingPings = new Map();
  }
  
  ping(host, port) {
    const pingId = crypto.randomBytes(8).toString('hex');
    const timestamp = Date.now();
    
    this.pendingPings.set(pingId, timestamp);
    
    const packet = JSON.stringify({
      type: 'ping',
      id: pingId,
      timestamp: timestamp
    });
    
    this.socket.send(packet, port, host);
    
    // Timeout after 5 seconds
    setTimeout(() => {
      if (this.pendingPings.has(pingId)) {
        console.log('⏱️  Ping timeout');
        this.pendingPings.delete(pingId);
      }
    }, 5000);
  }
  
  handlePong(packet) {
    const sendTime = this.pendingPings.get(packet.id);
    if (sendTime) {
      const latency = Date.now() - sendTime;
      console.log(`🏓 Pong received: ${latency}ms`);
      this.pendingPings.delete(packet.id);
      return latency;
    }
  }
}

// Server-side pong response
socket.on('message', (msg, rinfo) => {
  const packet = JSON.parse(msg.toString());
  if (packet.type === 'ping') {
    const pong = JSON.stringify({
      type: 'pong',
      id: packet.id,
      timestamp: Date.now()
    });
    socket.send(pong, rinfo.port, rinfo.address);
  }
});
3. Traffic Analysis
javascriptclass UDPTrafficAnalyzer {
  constructor() {
    this.socket = dgram.createSocket('udp4');
    this.traffic = {
      bytesReceived: 0,
      bytesSent: 0,
      packetsReceived: 0,
      packetsSent: 0,
      averagePacketSize: 0
    };
    
    // Report every 5 seconds
    setInterval(() => this.report(), 5000);
  }
  
  trackReceived(msg) {
    this.traffic.packetsReceived++;
    this.traffic.bytesReceived += msg.length;
    this.updateAverages();
  }
  
  trackSent(msg) {
    this.traffic.packetsSent++;
    this.traffic.bytesSent += msg.length;
    this.updateAverages();
  }
  
  updateAverages() {
    const totalPackets = this.traffic.packetsReceived + this.traffic.packetsSent;
    const totalBytes = this.traffic.bytesReceived + this.traffic.bytesSent;
    this.traffic.averagePacketSize = totalBytes / totalPackets;
  }
  
  report() {
    const inboundMbps = (this.traffic.bytesReceived * 8 / 1000000 / 5).toFixed(2);
    const outboundMbps = (this.traffic.bytesSent * 8 / 1000000 / 5).toFixed(2);
    
    console.log(`
📈 Traffic Report (last 5s):
   Inbound: ${inboundMbps} Mbps (${this.traffic.packetsReceived} packets)
   Outbound: ${outboundMbps} Mbps (${this.traffic.packetsSent} packets)
   Avg packet size: ${this.traffic.averagePacketSize.toFixed(0)} bytes
    `);
    
    // Reset counters
    this.traffic = {
      bytesReceived: 0,
      bytesSent: 0,
      packetsReceived: 0,
      packetsSent: 0,
      averagePacketSize: 0
    };
  }
}