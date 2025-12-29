# HTTP Headers - Complete Guide

## What are HTTP Headers?

**HTTP Headers** are metadata sent as key-value pairs along with HTTP requests and responses. They enable crucial functions like caching, authentication, state management, and content negotiation between clients and servers.

**Historical Note**: Before 2012, custom headers used the `X-` prefix (e.g., `X-Custom-Header`). This convention is now deprecated according to RFC 6648, and custom headers should use standard naming without the prefix.

---

## Categories of Headers

### 1. **Request Headers**
Sent from client to server, containing information about the request or the client itself.

### 2. **Response Headers**
Sent from server to client, providing information about the server or response.

### 3. **Representation Headers**
Describe the encoding, compression, and format of the message body.

### 4. **Payload Headers**
Contain information about the actual data being transmitted (size, type, range).

---

## Most Common Headers

### **Accept**
Specifies which content types (MIME types) the client can process. Example: `Accept: application/json, text/html`. Enables content negotiation between client and server.

### **User-Agent**
Identifies the client application, operating system, and vendor making the request. Helps servers optimize responses for different browsers and devices. Example: `User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)`.

### **Authorization**
Contains credentials to authenticate the client with the server. Common schemes include Bearer tokens, Basic auth, and API keys. Example: `Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...`

### **Content-Type**
Indicates the media type of the resource or data being sent. Essential for the receiver to correctly parse the body. Example: `Content-Type: application/json; charset=utf-8`.

### **Cookie**
Contains stored HTTP cookies previously sent by the server with `Set-Cookie`. Used for session management, personalization, and tracking. Example: `Cookie: sessionId=abc123; theme=dark`.

### **Cache-Control**
Directives for caching mechanisms in both requests and responses. Controls how, and for how long, responses are cached. Example: `Cache-Control: max-age=3600, must-revalidate`.

---

## CORS Headers (Cross-Origin Resource Sharing)

### **Access-Control-Allow-Origin**
Specifies which origins can access the resource. Use `*` for public APIs or specific domains for restricted access. Example: `Access-Control-Allow-Origin: https://example.com`.

### **Access-Control-Allow-Credentials**
Indicates whether the response can be exposed when credentials flag is true. Required when making credentialed requests (cookies, authorization headers). Example: `Access-Control-Allow-Credentials: true`.

### **Access-Control-Allow-Methods**
Specifies which HTTP methods are permitted when accessing the resource. Used in preflight requests. Example: `Access-Control-Allow-Methods: GET, POST, PUT, DELETE`.

### **Access-Control-Allow-Headers**
Lists which headers can be used in the actual request. Important for custom headers in cross-origin requests. Example: `Access-Control-Allow-Headers: Content-Type, Authorization`.

---

## Security Headers

### **Cross-Origin-Embedder-Policy (COEP)**
Prevents documents from loading cross-origin resources that don't explicitly grant permission. Enables powerful features like `SharedArrayBuffer`. Example: `Cross-Origin-Embedder-Policy: require-corp`.

### **Cross-Origin-Opener-Policy (COOP)**
Ensures a document is isolated from any cross-origin documents it opens. Protects against cross-origin attacks like Spectre. Example: `Cross-Origin-Opener-Policy: same-origin`.

### **Content-Security-Policy (CSP)**
Controls which resources the browser is allowed to load for a given page. Prevents XSS, clickjacking, and other code injection attacks. Example: `Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'`.

### **X-XSS-Protection**
Legacy header that enables browser's built-in XSS filtering. Mostly superseded by CSP but still used for older browsers. Example: `X-XSS-Protection: 1; mode=block`.

### **X-Frame-Options**
Prevents clickjacking by controlling whether a page can be displayed in a frame/iframe. Example: `X-Frame-Options: DENY` or `SAMEORIGIN`.

### **Strict-Transport-Security (HSTS)**
Forces browsers to use HTTPS connections only. Protects against protocol downgrade attacks and cookie hijacking. Example: `Strict-Transport-Security: max-age=31536000; includeSubDomains`.

---

## HTTP Methods

HTTP methods define the basic set of operations that can be used to interact with a server.

### **GET**
Retrieves data from the server. Should only fetch data without modifying anything. Safe and idempotent.

### **POST**
Submits data to the server to create a new resource. Often causes state change or side effects.

### **PUT**
Updates or replaces an existing resource completely. Idempotent - multiple identical requests have the same effect.

### **PATCH**
Partially updates an existing resource. Only sends changed fields rather than the entire resource.

### **DELETE**
Removes a specified resource from the server. Idempotent operation.

### **HEAD**
Same as GET but returns only headers without the response body. Useful for checking if a resource exists or getting metadata.

### **OPTIONS**
Describes communication options for the target resource. Used in CORS preflight requests to check allowed methods.

### **CONNECT**
Establishes a tunnel to the server, typically for HTTPS through an HTTP proxy.

### **TRACE**
Performs a message loop-back test along the path to the target resource. Used for debugging purposes.

---

## HTTP Status Codes

### **1xx - Informational Responses**

- **100 Continue**: Server received request headers, client should proceed to send body.
- **101 Switching Protocols**: Server is switching protocols as requested by the client (e.g., HTTP to WebSocket).
- **102 Processing**: Server has received and is processing the request, but no response is available yet.

### **2xx - Success**

- **200 OK**: Request succeeded. The meaning depends on the HTTP method used.
- **201 Created**: Request succeeded and a new resource was created. Common with POST/PUT requests.
- **202 Accepted**: Request accepted for processing but not yet completed. Used for asynchronous operations.
- **203 Non-Authoritative Information**: Request successful but returned metadata may be from a cached or third-party source.
- **204 No Content**: Request succeeded but no content to return. Often used with DELETE requests.
- **206 Partial Content**: Server is delivering only part of the resource due to a range header sent by the client.

### **3xx - Redirection**

- **301 Moved Permanently**: Resource permanently moved to a new URL. Future requests should use the new URL.
- **302 Found**: Resource temporarily moved to a different URL. Client should continue using original URL for future requests.
- **303 See Other**: Server redirects to another URL using GET method, regardless of original method.
- **304 Not Modified**: Resource hasn't changed since last request. Client can use cached version.
- **307 Temporary Redirect**: Similar to 302 but guarantees the request method won't change.
- **308 Permanent Redirect**: Similar to 301 but guarantees the request method won't change.

### **4xx - Client Errors**

- **400 Bad Request**: Server cannot process request due to client error (malformed syntax, invalid parameters).
- **401 Unauthorized**: Authentication required but not provided or failed. Client must authenticate.
- **403 Forbidden**: Server understands request but refuses to authorize it. Authentication won't help.
- **404 Not Found**: Server cannot find the requested resource. Most common error code.
- **405 Method Not Allowed**: Request method not supported for the requested resource.
- **408 Request Timeout**: Server timed out waiting for the request from client.
- **409 Conflict**: Request conflicts with current state of server (e.g., duplicate entry, version conflict).
- **410 Gone**: Resource permanently deleted and won't be available again.
- **413 Payload Too Large**: Request entity larger than server is willing to process.
- **415 Unsupported Media Type**: Server refuses request because payload format is unsupported.
- **422 Unprocessable Entity**: Request well-formed but contains semantic errors (validation failure).
- **429 Too Many Requests**: User has sent too many requests in a given time (rate limiting).

### **5xx - Server Errors**

- **500 Internal Server Error**: Generic server error. Something went wrong but server can't be more specific.
- **501 Not Implemented**: Server doesn't support functionality required to fulfill the request.
- **502 Bad Gateway**: Server acting as gateway received invalid response from upstream server.
- **503 Service Unavailable**: Server temporarily unable to handle request (overloaded or down for maintenance).
- **504 Gateway Timeout**: Server acting as gateway didn't receive timely response from upstream server.
- **505 HTTP Version Not Supported**: HTTP version used in request not supported by server.

---

## Additional Important Headers

### **Referer**
Contains the URL of the previous page that linked to the current request. Useful for analytics and security but raises privacy concerns.

### **Origin**
Indicates the origin (scheme, host, port) of the request. Used in CORS to determine if cross-origin request should be allowed.

### **Host**
Specifies the domain name and port number of the server. Required in HTTP/1.1 for virtual hosting.

### **If-Modified-Since**
Makes request conditional. Server returns resource only if modified after specified date. Works with 304 Not Modified.

### **ETag / If-None-Match**
ETag is a unique identifier for a specific version of a resource. If-None-Match uses ETag for conditional requests and caching.

### **Content-Length**
Indicates the size of the message body in bytes. Important for the receiver to know when the message is complete.

### **Content-Encoding**
Specifies compression algorithm applied to the resource (gzip, br, deflate). Client must decompress to use.

### **Set-Cookie**
Server sends cookies to be stored by client. Includes attributes like expiry, domain, path, secure, httpOnly, and SameSite.

### **Range / Content-Range**
Enables partial content requests. Useful for resuming downloads or loading large files in chunks.

---

## Best Practices

1. **Security First**: Always implement security headers (CSP, HSTS, X-Frame-Options) to protect against common vulnerabilities.

2. **Optimize Caching**: Use Cache-Control effectively to reduce server load and improve performance.

3. **CORS Configuration**: Avoid using `Access-Control-Allow-Origin: *` with credentials. Be specific about allowed origins.

4. **Content Negotiation**: Use Accept and Content-Type headers properly for API versioning and format negotiation.

5. **Error Handling**: Return appropriate status codes with meaningful error messages in the response body.

6. **Authentication**: Use secure methods like OAuth 2.0 or JWT tokens. Never send credentials in query parameters.

7. **Compression**: Enable gzip or brotli compression for text-based resources to reduce bandwidth usage.

8. **HTTPS Only**: Always use HTTPS in production and enable HSTS to enforce secure connections.

---

## Quick Reference Table

| Category | Purpose | Example Headers |
|----------|---------|----------------|
| Authentication | Verify identity | Authorization, WWW-Authenticate |
| Caching | Store responses | Cache-Control, ETag, Expires |
| CORS | Cross-origin access | Access-Control-Allow-* |
| Security | Protect against attacks | CSP, HSTS, X-Frame-Options |
| Content | Describe payload | Content-Type, Content-Length |
| Cookies | State management | Cookie, Set-Cookie |
| Conditional | Cache validation | If-Modified-Since, If-None-Match |

---

