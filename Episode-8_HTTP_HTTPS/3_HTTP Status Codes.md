## 3. HTTP Status Codes {#status-codes}

Status codes tell the client what happened with their request. They're grouped into five categories:

### 1xx: Informational (Request Received, Continuing Process)

**100 Continue**: Server received request headers, client should send body
```javascript
// Rarely used in modern applications
// Browser handles this automatically
```

**101 Switching Protocols**: Server switching to protocol requested by client (e.g., WebSocket upgrade)
```javascript
// WebSocket handshake
const ws = new WebSocket('ws://example.com/socket');
// Server responds with 101 Switching Protocols
```

### 2xx: Success (Request Successfully Received, Understood, and Accepted)

**200 OK**: Standard success response
```javascript
// Express.js
app.get('/api/users/:id', (req, res) => {
  const user = { id: req.params.id, name: 'John Doe' };
  res.status(200).json(user); // or just res.json(user)
});
```

**Real-Life Analogy**: You ordered a pizza, and it arrived exactly as requested.

**201 Created**: New resource successfully created
```javascript
app.post('/api/users', (req, res) => {
  const newUser = createUser(req.body);
  res.status(201)
     .location(`/api/users/${newUser.id}`) // URL of new resource
     .json(newUser);
});
```

**Real-Life Analogy**: You requested a new bank account, and it was successfully opened.

**204 No Content**: Success, but no content to return
```javascript
app.delete('/api/users/:id', (req, res) => {
  deleteUser(req.params.id);
  res.status(204).send(); // No body
});
```

**Real-Life Analogy**: You asked someone to turn off the lights, they did it, no need to confirm with words.

**206 Partial Content**: Server sending partial data (used for range requests, video streaming)
```javascript
// Client requests specific byte range
fetch('https://example.com/video.mp4', {
  headers: {
    'Range': 'bytes=0-1024'
  }
});

// Server responds with 206 and partial content
app.get('/video.mp4', (req, res) => {
  const range = req.headers.range;
  // ... process range request
  res.status(206)
     .header('Content-Range', 'bytes 0-1024/2048')
     .send(partialContent);
});
```

### 3xx: Redirection (Further Action Needed)

**301 Moved Permanently**: Resource permanently moved to new URL
```javascript
app.get('/old-page', (req, res) => {
  res.status(301)
     .location('/new-page')
     .send();
});
```

**Real-Life Analogy**: You moved houses and filed a change of address with the post office. All mail is permanently forwarded to your new address.

**302 Found** (Temporary Redirect): Resource temporarily at different URL
```javascript
app.get('/maintenance', (req, res) => {
  if (maintenanceMode) {
    res.redirect(302, '/maintenance-page'); // or just res.redirect()
  }
});
```

**304 Not Modified**: Cached version is still valid (used with If-Modified-Since)
```javascript
app.get('/api/data', (req, res) => {
  const lastModified = new Date('2025-10-01');
  const ifModifiedSince = req.headers['if-modified-since'];
  
  if (ifModifiedSince && new Date(ifModifiedSince) >= lastModified) {
    res.status(304).send(); // Use cached version
  } else {
    res.status(200)
       .header('Last-Modified', lastModified.toUTCString())
       .json(data);
  }
});
```

**Real-Life Analogy**: You check your mailbox, but there's a note saying "No new mail since you last checked."

**307 Temporary Redirect**: Like 302, but method must not change
**308 Permanent Redirect**: Like 301, but method must not change

### 4xx: Client Errors (Request Contains Bad Syntax or Cannot Be Fulfilled)

**400 Bad Request**: Server couldn't understand request
```javascript
app.post('/api/users', (req, res) => {
  const { email } = req.body;
  
  if (!email || !isValidEmail(email)) {
    return res.status(400).json({
      error: 'Bad Request',
      message: 'Valid email is required'
    });
  }
  
  // Process valid request
});
```

**Real-Life Analogy**: You filled out a form with gibberish, and the clerk can't process it.

**401 Unauthorized**: Authentication required or failed
```javascript
app.get('/api/protected', (req, res) => {
  const token = req.headers.authorization;
  
  if (!token || !isValidToken(token)) {
    return res.status(401)
              .header('WWW-Authenticate', 'Bearer realm="api"')
              .json({
                error: 'Unauthorized',
                message: 'Valid authentication token required'
              });
  }
  
  // Process authenticated request
});
```

**Real-Life Analogy**: You try to enter a members-only club, but you don't have a membership card.

**403 Forbidden**: Server understood but refuses to authorize
```javascript
app.delete('/api/users/:id', (req, res) => {
  const currentUser = getCurrentUser(req);
  const targetUserId = req.params.id;
  
  if (!currentUser.isAdmin && currentUser.id !== targetUserId) {
    return res.status(403).json({
      error: 'Forbidden',
      message: 'You do not have permission to delete this user'
    });
  }
  
  // Process authorized request
});
```

**Real-Life Analogy**: You have a library card (authenticated), but you're trying to access the staff-only section (not authorized).

**401 vs 403 Explained**:
- **401**: "Who are you?" - You need to prove your identity
- **403**: "I know who you are, but you can't do that" - Insufficient permissions

**404 Not Found**: Resource doesn't exist
```javascript
app.get('/api/users/:id', (req, res) => {
  const user = findUserById(req.params.id);
  
  if (!user) {
    return res.status(404).json({
      error: 'Not Found',
      message: `User with id ${req.params.id} not found`
    });
  }
  
  res.json(user);
});
```

**Real-Life Analogy**: You're looking for a book in the library, but it's not on the shelf where it should be.

**405 Method Not Allowed**: HTTP method not supported for this resource
```javascript
app.get('/api/users', (req, res) => {
  // GET is allowed
  res.json(users);
});

// No POST handler defined
app.post('/api/users', (req, res) => {
  res.status(405)
     .header('Allow', 'GET, OPTIONS') // Tell client what's allowed
     .json({
       error: 'Method Not Allowed',
       message: 'POST method is not supported for this endpoint'
     });
});
```

**408 Request Timeout**: Server timed out waiting for request
```javascript
const server = app.listen(3000);
server.timeout = 30000; // 30 seconds

// If client doesn't send complete request within 30s, server responds with 408
```

**409 Conflict**: Request conflicts with current state
```javascript
app.post('/api/users', async (req, res) => {
  const { email } = req.body;
  const existingUser = await findUserByEmail(email);
  
  if (existingUser) {
    return res.status(409).json({
      error: 'Conflict',
      message: 'User with this email already exists'
    });
  }
  
  // Create new user
});
```

**Real-Life Analogy**: You're trying to create a new account with a username that's already taken.

**429 Too Many Requests**: Rate limit exceeded
```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
  handler: (req, res) => {
    res.status(429).json({
      error: 'Too Many Requests',
      message: 'Rate limit exceeded. Try again later.',
      retryAfter: res.getHeader('Retry-After')
    });
  }
});

app.use('/api/', limiter);
```

### 5xx: Server Errors (Server Failed to Fulfill Valid Request)

**500 Internal Server Error**: Generic server error
```javascript
app.get('/api/users', async (req, res) => {
  try {
    const users = await database.query('SELECT * FROM users');
    res.json(users);
  } catch (error) {
    console.error('Database error:', error);
    res.status(500).json({
      error: 'Internal Server Error',
      message: 'An unexpected error occurred'
      // Don't expose error details to client in production
    });
  }
});
```

**Real-Life Analogy**: You ordered food at a restaurant, but the kitchen caught fire. It's not your fault.

**501 Not Implemented**: Server doesn't support functionality
```javascript
app.patch('/api/users/:id', (req, res) => {
  res.status(501).json({
    error: 'Not Implemented',
    message: 'PATCH method is not yet implemented'
  });
});
```

**502 Bad Gateway**: Server received invalid response from upstream server
```javascript
// When your server acts as a proxy
app.get('/api/external', async (req, res) => {
  try {
    const response = await fetch('https://external-api.com/data');
    
    if (!response.ok) {
      // External API returned error
      return res.status(502).json({
        error: 'Bad Gateway',
        message: 'Upstream server returned an error'
      });
    }
    
    const data = await response.json();
    res.json(data);
  } catch (error) {
    res.status(502).json({
      error: 'Bad Gateway',
      message: 'Could not reach upstream server'
    });
  }
});
```

**Real-Life Analogy**: You called a call center, and they tried to transfer you to another department, but the line went dead.

**503 Service Unavailable**: Server temporarily unavailable (maintenance, overload)
```javascript
let isMaintenanceMode = false;

app.use((req, res, next) => {
  if (isMaintenanceMode) {
    return res.status(503)
              .header('Retry-After', '3600') // Try again in 1 hour
              .json({
                error: 'Service Unavailable',
                message: 'Server is under maintenance. Please try again later.'
              });
  }
  next();
});
```

**504 Gateway Timeout**: Server didn't receive timely response from upstream
```javascript
app.get('/api/slow-external', async (req, res) => {
  try {
    const controller = new AbortController();
    const timeout = setTimeout(() => controller.abort(), 5000); // 5 second timeout
    
    const response = await fetch('https://slow-api.com/data', {
      signal: controller.signal
    });
    
    clearTimeout(timeout);
    const data = await response.json();
    res.json(data);
  } catch (error) {
    if (error.name === 'AbortError') {
      return res.status(504).json({
        error: 'Gateway Timeout',
        message: 'Upstream server took too long to respond'
      });
    }
    res.status(500).json({ error: 'Internal Server Error' });
  }
});
```

### Status Code Categories Summary

**Real-Life Analogy for Each Category**:
- **1xx**: "I heard you, give me a moment..." (Information)
- **2xx**: "Done! Here's what you asked for." (Success)
- **3xx**: "What you're looking for is over there." (Redirection)
- **4xx**: "You made a mistake in your request." (Client Error)
- **5xx**: "I messed up on my end." (Server Error)

### Handling Status Codes in Frontend

```javascript
// Fetch API
fetch('https://api.example.com/users')
  .then(response => {
    if (!response.ok) {
      // Handle error based on status code
      switch(response.status) {
        case 400:
          throw new Error('Bad request');
        case 401:
          // Redirect to login
          window.location.href = '/login';
          break;
        case 403:
          throw new Error('Access denied');
        case 404:
          throw new Error('Resource not found');
        case 429:
          throw new Error('Too many requests. Please slow down.');
        case 500:
        case 502:
        case 503:
        case 504:
          throw new Error('Server error. Please try again later.');
        default:
          throw new Error(`Unexpected error: ${response.status}`);
      }
    }
    return response.json();
  })
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

```javascript
// Axios (automatically rejects on error status codes)
axios.get('https://api.example.com/users')
  .then(response => console.log(response.data))
  .catch(error => {
    if (error.response) {
      // Server responded with error status
      const status = error.response.status;
      const message = error.response.data.message;
      
      if (status === 401) {
        // Handle authentication error
        redirectToLogin();
      } else if (status >= 500) {
        // Handle server error
        showErrorNotification('Server error. Please try again later.');
      } else {
        // Handle other errors
        showErrorNotification(message);
      }
    } else if (error.request) {
      // Request made but no response received
      showErrorNotification('No response from server. Check your connection.');
    } else {
      // Error in request setup
      showErrorNotification('Request failed: ' + error.message);
    }
  });
```

---
