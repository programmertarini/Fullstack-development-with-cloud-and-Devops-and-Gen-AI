Practical Developer Scenarios
Scenario 1: Building a Real-Time Chat Application
Decision: Which protocol to use?
Analysis:

Messages must arrive: TCP ✓
Order matters: TCP ✓
Latency is acceptable: Small delay for reliability is fine
Binary decision: Can't lose chat messages

Implementation:
javascript// Use WebSocket (built on TCP)
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8080 });

wss.on('connection', (ws) => {
  ws.on('message', (message) => {
    // TCP ensures this arrives reliably and in order
    broadcast(message);
  });
});
Scenario 2: Building a Live Sports Score Update App
Decision: Which protocol to use?
Analysis:

Real-time updates: UDP-friendly
Occasional loss acceptable: Missing one score update okay
High frequency: Scores update every few seconds
Many concurrent users: UDP scales better

Implementation:
javascript// Use UDP for score broadcasts
const dgram = require('dgram');
const socket = dgram.createSocket('udp4');

function broadcastScore(team1Score, team2Score) {
  const update = JSON.stringify({
    type: 'score_update',
    team1: team1Score,
    team2: team2Score,
    timestamp: Date.now()
  });
  
  // Broadcast to all clients
  clients.forEach(client => {
    socket.send(update, client.port, client.address);
  });
}

// Client can miss one update; next update arrives soon
Scenario 3: Building a File Sync Service (like Dropbox)
Decision: Which protocol to use?
Analysis:

File integrity critical: TCP ✓
Cannot lose data: TCP ✓
Speed less critical: Reliability more important
Large files: Need chunking and reliability

Implementation:
javascript// Use TCP/HTTP for file transfers
const https = require('https');
const fs = require('fs');

function uploadFile(filePath) {
  const fileStream = fs.createReadStream(filePath);
  
  const options = {
    method: 'POST',
    hostname: 'api.filesync.com',
    path: '/upload',
    headers: { 'Content-Type': 'application/octet-stream' }
  };
  
  const req = https.request(options, (res) => {
    // TCP ensures entire file arrives intact
    console.log('Upload complete');
  });
  
  fileStream.pipe(req);
}
Scenario 4: Building a Network Monitoring Tool
Decision: Which protocol to use?
Analysis:

Need ICMP (ping): ICMP/UDP
Speed critical: Fast responses needed
Loss acceptable: Can retry ping if no response
Lightweight: Minimal overhead desired

Implementation:
javascript// Use UDP for network monitoring
const dgram = require('dgram');
const socket = dgram.createSocket('udp4');

function pingHost(host, port) {
  const startTime = Date.now();
  
  socket.send('PING', port, host);
  
  socket.once('message', (msg) => {
    const latency = Date.now() - startTime;
    console.log(`Host ${host} responded in ${latency}ms`);
  });
  
  // Timeout after 5 seconds
  setTimeout(() => {
    console.log(`Host ${host} did not respond`);
  }, 5000);
}