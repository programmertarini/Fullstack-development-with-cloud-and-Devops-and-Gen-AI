## 2. HTTP Request-Response Cycle {#request-response-cycle}

### The Complete Cycle

The HTTP request-response cycle is a conversation between a client and server. Here's what happens step-by-step:

```
Client                                Server
  |                                      |
  |  1. DNS Lookup (resolve domain)     |
  |------------------------------------->|
  |                                      |
  |  2. TCP Connection (3-way handshake)|
  |<------------------------------------>|
  |                                      |
  |  3. HTTP Request                    |
  |------------------------------------->|
  |                                      |
  |                          4. Process Request
  |                                      |
  |  5. HTTP Response                   |
  |<-------------------------------------|
  |                                      |
  |  6. Connection Close (or Keep-Alive)|
  |<------------------------------------>|
```

### Step-by-Step Breakdown

#### Step 1: DNS Lookup
When you type `www.example.com`, your browser needs to find the server's IP address.

**Real-Life Analogy**: Like looking up a phone number in a directory before calling someone.

```javascript
// Browser does this automatically, but you can do DNS lookup in Node.js
const dns = require('dns');

dns.lookup('www.example.com', (err, address, family) => {
  console.log('IP address:', address); // e.g., 93.184.216.34
});
```

#### Step 2: TCP Connection (3-Way Handshake)
Before sending HTTP data, a TCP connection is established:

1. **SYN**: Client sends synchronization request
2. **SYN-ACK**: Server acknowledges and sends its own sync
3. **ACK**: Client acknowledges server's sync

**Real-Life Analogy**: Like a phone conversation:
- You: "Hello, can you hear me?" (SYN)
- Friend: "Yes, I can hear you! Can you hear me?" (SYN-ACK)
- You: "Yes, I can!" (ACK)
- Now the conversation begins

#### Step 3: HTTP Request
The client sends an HTTP request with three main parts:

##### a) Request Line
```
GET /api/users/123 HTTP/1.1
```
- **Method**: GET
- **Path**: /api/users/123
- **Protocol Version**: HTTP/1.1

##### b) Request Headers
```
Host: api.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: application/json
Accept-Language: en-US,en;q=0.9
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json
Cookie: sessionId=abc123xyz
```

**Common Headers Explained**:
- **Host**: The domain name of the server (required in HTTP/1.1)
- **User-Agent**: Information about the client (browser, device)
- **Accept**: Types of content the client can process
- **Authorization**: Credentials for authentication
- **Content-Type**: Format of the request body
- **Cookie**: Previously stored data from the server

##### c) Request Body (for POST, PUT, PATCH)
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "age": 30
}
```

**Example HTTP Request (Complete)**:
```javascript
// Using fetch API in the browser
fetch('https://api.example.com/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer your-token-here'
  },
  body: JSON.stringify({
    name: 'John Doe',
    email: 'john@example.com',
    age: 30
  })
})
.then(response => response.json())
.then(data => console.log(data));
```

```javascript
// Using axios in Node.js
const axios = require('axios');

axios.post('https://api.example.com/api/users', {
  name: 'John Doe',
  email: 'john@example.com',
  age: 30
}, {
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer your-token-here'
  }
})
.then(response => console.log(response.data))
.catch(error => console.error(error));
```

#### Step 4: Server Processing
The server:
1. Parses the request
2. Validates authentication/authorization
3. Executes business logic
4. Queries databases if needed
5. Prepares the response

```javascript
// Express.js server example
const express = require('express');
const app = express();

app.use(express.json()); // Parse JSON bodies

app.post('/api/users', (req, res) => {
  // Step 1: Parse request (done by express.json())
  const { name, email, age } = req.body;
  
  // Step 2: Validate
  if (!name || !email) {
    return res.status(400).json({ error: 'Name and email are required' });
  }
  
  // Step 3: Business logic (simplified)
  const newUser = {
    id: Date.now(),
    name,
    email,
    age: age || null,
    createdAt: new Date()
  };
  
  // Step 4: Database operation (simulated)
  // await db.users.insert(newUser);
  
  // Step 5: Send response
  res.status(201).json({
    message: 'User created successfully',
    user: newUser
  });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

#### Step 5: HTTP Response
The server sends back a response with three main parts:

##### a) Status Line
```
HTTP/1.1 200 OK
```
- **Protocol Version**: HTTP/1.1
- **Status Code**: 200
- **Status Text**: OK

##### b) Response Headers
```
Content-Type: application/json
Content-Length: 156
Date: Mon, 13 Oct 2025 10:30:00 GMT
Server: nginx/1.18.0
Set-Cookie: sessionId=xyz789; HttpOnly; Secure
Cache-Control: no-cache, no-store, must-revalidate
Access-Control-Allow-Origin: https://frontend.example.com
X-RateLimit-Remaining: 99
```

**Common Headers Explained**:
- **Content-Type**: Format of the response body
- **Content-Length**: Size of the response body in bytes
- **Date**: When the response was generated
- **Server**: Software running on the server
- **Set-Cookie**: Stores data on the client
- **Cache-Control**: Instructions for caching
- **Access-Control-Allow-Origin**: CORS policy

##### c) Response Body
```json
{
  "message": "User created successfully",
  "user": {
    "id": 1697195400000,
    "name": "John Doe",
    "email": "john@example.com",
    "age": 30,
    "createdAt": "2025-10-13T10:30:00.000Z"
  }
}
```

#### Step 6: Connection Management

**Connection: close** - Connection terminates after response
```
Connection: close
```

**Connection: keep-alive** - Connection stays open for subsequent requests (HTTP/1.1 default)
```
Connection: keep-alive
Keep-Alive: timeout=5, max=100
```

**Real-Life Analogy**: 
- **close**: Like hanging up the phone after each sentence and calling back
- **keep-alive**: Like staying on the line for the entire conversation

### HTTP Versions Evolution

#### HTTP/0.9 (1991)
- Only GET method
- No headers
- Only HTML responses

#### HTTP/1.0 (1996)
- Added headers
- Added status codes
- Added methods (POST, HEAD)
- Each request required a new TCP connection

#### HTTP/1.1 (1997)
- **Persistent connections** (keep-alive by default)
- **Pipelining**: Send multiple requests without waiting for responses
- **Chunked transfer encoding**
- **Host header** (enables virtual hosting)

#### HTTP/2 (2015)
- **Binary protocol** (not text-based)
- **Multiplexing**: Multiple requests/responses over single connection
- **Server push**: Server can send resources before client requests
- **Header compression**: Reduces overhead

#### HTTP/3 (2022)
- Uses **QUIC** instead of TCP
- Faster connection establishment
- Better handling of packet loss
- Improved performance on poor networks

```javascript
// Checking HTTP version in Node.js
const https = require('https');

https.get('https://www.example.com', (res) => {
  console.log('HTTP Version:', res.httpVersion); // e.g., "2.0"
});
```

---