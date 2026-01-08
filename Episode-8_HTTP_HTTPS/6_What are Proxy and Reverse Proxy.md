# Proxy, Reverse Proxy

## 1. Proxy Server

### What is a Proxy?

A **proxy server** acts as an intermediary between a client (your computer/application) and the destination server (website/service). When you make a request, it goes to the proxy first, which then forwards it to the destination server. The response follows the same path back.

**Flow**: `Client → Proxy → Destination Server → Proxy → Client`

### Key Characteristics

- **Client-side intermediary**: Works on behalf of the client
- **Hides client identity**: The destination server sees the proxy's IP, not yours
- **Request forwarding**: Intercepts and forwards client requests
- **Single point of control**: Multiple clients can use the same proxy

### Types of Proxies

#### Forward Proxy (Standard Proxy)
The most common type. Sits between clients and the internet.

**Use Cases**:
- Bypassing geo-restrictions (accessing content blocked in your region)
- Anonymous browsing (hiding your IP address)
- Content filtering (blocking certain websites in organizations)
- Caching frequently accessed content (improving speed)
- Bandwidth monitoring and control

#### Transparent Proxy
Intercepts requests without client configuration. The client doesn't know it's being proxied.

**Use Cases**:
- Corporate networks for monitoring
- ISP-level content filtering
- Caching at network level

#### Anonymous Proxy
Hides your IP but identifies itself as a proxy.

#### High Anonymity Proxy (Elite Proxy)
Hides your IP and doesn't identify itself as a proxy. Appears as a regular client.

### How Proxy Works (Technical Flow)

1. **Client Configuration**: Client is configured to send requests to proxy (e.g., browser proxy settings)
2. **Request Interception**: Client sends HTTP/HTTPS request to proxy instead of destination
3. **Proxy Processing**: Proxy examines the request, may modify headers, cache check
4. **Request Forwarding**: Proxy forwards request to actual destination server
5. **Response Handling**: Proxy receives response from destination
6. **Response Delivery**: Proxy sends response back to client (may cache it)

### Proxy Headers

Proxies typically add headers to requests:

```http
X-Forwarded-For: client_ip_address
X-Forwarded-Proto: http/https
X-Forwarded-Host: original_host
Via: proxy_server_info
```

### Implementation Considerations for Developers

**When building applications that work through proxies**:

- **Respect proxy headers**: Check `X-Forwarded-For` to get real client IP
- **Handle proxy authentication**: Support proxy credentials if needed
- **SSL/TLS considerations**: HTTPS through proxy requires CONNECT method
- **Timeout management**: Proxy adds latency, adjust timeouts accordingly
- **WebSocket support**: Not all proxies support WebSocket connections

**Simple proxy setup example** (Node.js):

```javascript
const http = require('http');
const request = require('request');

http.createServer((clientReq, clientRes) => {
  request(clientReq.url).pipe(clientRes);
}).listen(8080);
```

### Limitations

- **SSL/TLS complexity**: HTTPS requires additional handling (CONNECT tunneling)
- **Single point of failure**: If proxy goes down, all clients are affected
- **Latency**: Additional hop adds delay
- **Trust requirement**: Proxy can see all your traffic (for HTTP)

---

## 2. Reverse Proxy

### What is a Reverse Proxy?

A **reverse proxy** sits in front of web servers and forwards client requests to the appropriate backend server. Unlike a forward proxy (which serves clients), a reverse proxy serves servers.

**Flow**: `Client → Reverse Proxy → Backend Server(s) → Reverse Proxy → Client`

### Key Characteristics

- **Server-side intermediary**: Works on behalf of servers, not clients
- **Hides server identity**: Clients only see the reverse proxy, not backend servers
- **Single entry point**: All traffic enters through the reverse proxy
- **Transparent to clients**: Clients think they're communicating directly with origin server

### Core Functions

#### Load Balancing
Distributes incoming requests across multiple backend servers.

**Algorithms**:
- **Round Robin**: Requests distributed sequentially
- **Least Connections**: Sends to server with fewest active connections
- **IP Hash**: Same client always goes to same server (session persistence)
- **Weighted**: Servers get requests proportional to their capacity

#### SSL/TLS Termination
Handles SSL encryption/decryption, reducing load on backend servers.

**Benefits**:
- Centralized certificate management
- Offloads CPU-intensive encryption from application servers
- Easier to update SSL configurations

#### Caching
Stores responses to reduce backend server load and improve response times.

**Strategies**:
- Static content caching (images, CSS, JavaScript)
- API response caching (with proper cache headers)
- Intelligent cache invalidation

#### Security

- **DDoS protection**: Can absorb and filter malicious traffic
- **WAF integration**: Web Application Firewall at proxy level
- **Rate limiting**: Prevent abuse by limiting requests per client
- **IP blacklisting/whitelisting**: Control access at entry point

### Real-World Architecture

```
Internet
   ↓
[Reverse Proxy - Nginx/HAProxy]
   ↓
   ├─→ [Web Server 1]
   ├─→ [Web Server 2]
   ├─→ [Web Server 3]
   └─→ [API Server]
```

### Popular Reverse Proxy Solutions

#### Nginx
- High performance, low resource usage
- Excellent for static content
- Built-in load balancing and caching

#### HAProxy
- Advanced load balancing features
- Health checking and failover
- TCP and HTTP load balancing

#### Apache with mod_proxy
- Flexible configuration
- Wide module ecosystem

#### Cloud Solutions
- AWS Application Load Balancer (ALB)
- Google Cloud Load Balancing
- Cloudflare (CDN with reverse proxy features)

### Configuration Example (Nginx)

```nginx
upstream backend {
    server backend1.example.com:8080 weight=3;
    server backend2.example.com:8080;
    server backend3.example.com:8080 backup;
}

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Developer Considerations

**When building applications behind reverse proxy**:

- **Trust proxy headers**: Read `X-Forwarded-For`, `X-Real-IP` for client info
- **Health check endpoints**: Create endpoints for proxy health checking
- **Session affinity**: Use sticky sessions if application is stateful
- **Logging**: Log forwarded headers to track real client IPs
- **Base URL handling**: Application should work regardless of proxy path rewriting

### Use Cases

1. **Microservices Architecture**: Route requests to different services based on path
2. **Blue-Green Deployments**: Switch traffic between versions instantly
3. **A/B Testing**: Route percentage of traffic to new features
4. **API Gateway**: Single entry point for multiple APIs
5. **CDN Origin**: Serve as origin server for CDN distribution

### Proxy vs Reverse Proxy

| Aspect | Forward Proxy | Reverse Proxy |
|--------|--------------|---------------|
| **Position** | Client-side | Server-side |
| **Serves** | Clients | Servers |
| **Hides** | Client identity | Server identity |
| **Configuration** | Client must configure | Transparent to client |
| **Use Case** | Anonymity, access control | Load balancing, security |

---
