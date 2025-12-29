## 5. Key Concepts Deep Dive {#key-concepts}

### HTTP Request Methods (Verbs)

HTTP methods define the type of action to be performed on a resource. They should be used according to RESTful principles.

#### GET - Retrieve Data

**Purpose**: Fetch a resource without modifying it

**Characteristics**:

- **Safe**: Doesn't modify server state
- **Idempotent**: Multiple identical requests have same effect as single request
- **Cacheable**: Responses can be cached
- **Parameters in URL**: Data sent via query string

**Real-Life Analogy**: Like looking at a book in a library. You can read it multiple times without changing it.

```javascript
// Client-side
// Simple GET request
fetch("https://api.example.com/users/123")
  .then((res) => res.json())
  .then((data) => console.log(data));

// GET with query parameters
const params = new URLSearchParams({
  page: 1,
  limit: 10,
  sort: "name",
  filter: "active",
});

fetch(`https://api.example.com/users?${params}`)
  .then((res) => res.json())
  .then((data) => console.log(data));

// Server-side (Express)
app.get("/users", (req, res) => {
  // Access query parameters
  const { page = 1, limit = 10, sort, filter } = req.query;

  // Fetch users from database
  const users = getUsersFromDB({ page, limit, sort, filter });

  res.json({
    data: users,
    pagination: {
      page: parseInt(page),
      limit: parseInt(limit),
      total: getTotalUsers(),
    },
  });
});

app.get("/users/:id", (req, res) => {
  // Access URL parameters
  const userId = req.params.id;
  const user = getUserById(userId);

  if (!user) {
    return res.status(404).json({ error: "User not found" });
  }

  res.json(user);
});
```

**Best Practices**:

- Use for read operations only
- Never use GET for sensitive data (passwords, API keys) - data visible in URL
- Keep URLs under 2048 characters
- Use query parameters for filtering, sorting, pagination

#### POST - Create New Resource

**Purpose**: Submit data to create a new resource

**Characteristics**:

- **Not Safe**: Modifies server state
- **Not Idempotent**: Multiple requests create multiple resources
- **Not Cacheable**: (usually)
- **Parameters in Body**: Data sent in request body

**Real-Life Analogy**: Like submitting a form to create a new bank account. Each submission creates a different account.

```javascript
// Client-side
fetch("https://api.example.com/users", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Authorization: "Bearer token123",
  },
  body: JSON.stringify({
    name: "John Doe",
    email: "john@example.com",
    password: "securePassword123",
  }),
})
  .then((res) => {
    if (res.status === 201) {
      return res.json();
    }
    throw new Error("Failed to create user");
  })
  .then((data) => {
    console.log("User created:", data);
    // Typically contains: { id: 123, name: 'John Doe', ... }
  });

// Server-side (Express)
app.post("/users", async (req, res) => {
  // Validate input
  const { name, email, password } = req.body;

  if (!name || !email || !password) {
    return res.status(400).json({
      error: "Missing required fields",
    });
  }

  // Check if email already exists
  const existingUser = await findUserByEmail(email);
  if (existingUser) {
    return res.status(409).json({
      error: "Email already registered",
    });
  }

  // Hash password
  const hashedPassword = await bcrypt.hash(password, 10);

  // Create user
  const newUser = await createUser({
    name,
    email,
    password: hashedPassword,
  });

  // Return 201 Created with Location header
  res.status(201).location(`/users/${newUser.id}`).json({
    id: newUser.id,
    name: newUser.name,
    email: newUser.email,
    // Don't return password!
  });
});
```

**Best Practices**:

- Return 201 Created on success
- Include Location header with URL of new resource
- Return created resource in response body
- Don't return sensitive data (passwords)

#### PUT - Update/Replace Resource

**Purpose**: Replace entire resource or create if doesn't exist

**Characteristics**:

- **Not Safe**: Modifies server state
- **Idempotent**: Multiple identical requests have same effect
- **Complete Replacement**: Entire resource is replaced

**Real-Life Analogy**: Like replacing a page in a document. You remove the old page and put in a completely new one.

```javascript
// Client-side
fetch("https://api.example.com/users/123", {
  method: "PUT",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    name: "John Doe Updated",
    email: "john.updated@example.com",
    age: 31,
    bio: "Full-stack developer",
    // Must include ALL fields, even if unchanged
  }),
})
  .then((res) => res.json())
  .then((data) => console.log("User updated:", data));

// Server-side (Express)
app.put("/users/:id", async (req, res) => {
  const userId = req.params.id;
  const userData = req.body;

  // Validate all required fields are present
  const requiredFields = ["name", "email", "age", "bio"];
  const missingFields = requiredFields.filter((field) => !userData[field]);

  if (missingFields.length > 0) {
    return res.status(400).json({
      error: `Missing fields: ${missingFields.join(", ")}`,
    });
  }

  // Check if user exists
  const existingUser = await findUserById(userId);

  if (!existingUser) {
    // PUT can create resource if it doesn't exist
    const newUser = await createUser({ id: userId, ...userData });
    return res.status(201).json(newUser);
  }

  // Replace entire user object
  const updatedUser = await replaceUser(userId, userData);
  res.json(updatedUser);
});
```

#### PATCH - Partial Update

**Purpose**: Update specific fields of a resource

**Characteristics**:

- **Not Safe**: Modifies server state
- **Idempotent**: (should be, but depends on implementation)
- **Partial Update**: Only specified fields are modified

**Real-Life Analogy**: Like using correction fluid on a document. You only fix the mistakes, not rewrite the whole page.

```javascript
// Client-side
fetch("https://api.example.com/users/123", {
  method: "PATCH",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    email: "newemail@example.com",
    // Only fields to update
  }),
})
  .then((res) => res.json())
  .then((data) => console.log("User updated:", data));

// Server-side (Express)
app.patch("/users/:id", async (req, res) => {
  const userId = req.params.id;
  const updates = req.body;

  // Check if user exists
  const user = await findUserById(userId);
  if (!user) {
    return res.status(404).json({ error: "User not found" });
  }

  // Validate only provided fields
  const allowedFields = ["name", "email", "age", "bio"];
  const invalidFields = Object.keys(updates).filter(
    (field) => !allowedFields.includes(field)
  );

  if (invalidFields.length > 0) {
    return res.status(400).json({
      error: `Invalid fields: ${invalidFields.join(", ")}`,
    });
  }

  // Update only provided fields
  const updatedUser = await updateUser(userId, updates);
  res.json(updatedUser);
});
```

**PUT vs PATCH**:

```javascript
// Current user state
{
  id: 123,
  name: "John Doe",
  email: "john@example.com",
  age: 30,
  bio: "Developer"
}

// PUT request (must include all fields)
PUT /users/123
{
  name: "John Doe",
  email: "newemail@example.com", // Changed
  age: 30,
  bio: "Developer"
}
// Result: Entire object replaced

// PATCH request (only changed fields)
PATCH /users/123
{
  email: "newemail@example.com" // Only this changed
}
// Result: Only email field updated, others unchanged
```

#### DELETE - Remove Resource

**Purpose**: Delete a resource

**Characteristics**:

- **Not Safe**: Modifies server state
- **Idempotent**: Deleting same resource multiple times has same result (resource is gone)

**Real-Life Analogy**: Like shredding a document. Once it's done, doing it again doesn't change anything (document is already gone).

```javascript
// Client-side
fetch("https://api.example.com/users/123", {
  method: "DELETE",
  headers: {
    Authorization: "Bearer token123",
  },
}).then((res) => {
  if (res.status === 204) {
    console.log("User deleted successfully");
  } else if (res.status === 404) {
    console.log("User not found");
  }
});

// Server-side (Express)
app.delete("/users/:id", async (req, res) => {
  const userId = req.params.id;

  // Check if user exists
  const user = await findUserById(userId);
  if (!user) {
    return res.status(404).json({ error: "User not found" });
  }

  // Check authorization
  const currentUser = getCurrentUser(req);
  if (!currentUser.isAdmin && currentUser.id !== userId) {
    return res.status(403).json({
      error: "Not authorized to delete this user",
    });
  }

  // Delete user
  await deleteUser(userId);

  // Return 204 No Content (no response body needed)
  res.status(204).send();

  // OR return 200 with confirmation message
  // res.json({ message: 'User deleted successfully' });
});

// Soft delete (mark as deleted instead of removing)
app.delete("/users/:id", async (req, res) => {
  const userId = req.params.id;

  await updateUser(userId, {
    deleted: true,
    deletedAt: new Date(),
  });

  res.status(204).send();
});
```

#### HEAD - Get Headers Only

**Purpose**: Same as GET but without response body

**Use Cases**:

- Check if resource exists
- Get resource metadata (size, last modified)
- Check if cached version is still valid

```javascript
// Client-side
fetch("https://api.example.com/large-file.pdf", {
  method: "HEAD",
}).then((res) => {
  console.log("File size:", res.headers.get("content-length"));
  console.log("Last modified:", res.headers.get("last-modified"));
  console.log("Content type:", res.headers.get("content-type"));
});

// Server-side (Express)
app.head("/files/:filename", (req, res) => {
  const filename = req.params.filename;
  const filePath = path.join(__dirname, "files", filename);

  if (!fs.existsSync(filePath)) {
    return res.status(404).end();
  }

  const stats = fs.statSync(filePath);
  res.set({
    "Content-Type": mime.lookup(filename),
    "Content-Length": stats.size,
    "Last-Modified": stats.mtime.toUTCString(),
  });

  res.end(); // No body
});
```

#### OPTIONS - Get Allowed Methods

**Purpose**: Discover what methods are allowed for a resource

**Use Cases**:

- CORS preflight requests
- API discovery

```javascript
// Server-side (Express)
app.options("/users/:id", (req, res) => {
  res.set({
    Allow: "GET, PUT, PATCH, DELETE, OPTIONS",
    "Access-Control-Allow-Methods": "GET, PUT, PATCH, DELETE, OPTIONS",
    "Access-Control-Allow-Headers": "Content-Type, Authorization",
  });
  res.status(204).send();
});

// CORS preflight handling (automatic with cors middleware)
const cors = require("cors");

app.use(
  cors({
    origin: "https://frontend.example.com",
    methods: ["GET", "POST", "PUT", "PATCH", "DELETE"],
    allowedHeaders: ["Content-Type", "Authorization"],
  })
);
```

### Method Summary Table

| Method  | Purpose  | Safe | Idempotent | Cacheable | Body     |
| ------- | -------- | ---- | ---------- | --------- | -------- |
| GET     | Retrieve | ✅   | ✅         | ✅        | No       |
| POST    | Create   | ❌   | ❌         | ❌        | Yes      |
| PUT     | Replace  | ❌   | ✅         | ❌        | Yes      |
| PATCH   | Update   | ❌   | ✅\*       | ❌        | Yes      |
| DELETE  | Remove   | ❌   | ✅         | ❌        | Optional |
| HEAD    | Headers  | ✅   | ✅         | ✅        | No       |
| OPTIONS | Methods  | ✅   | ✅         | ❌        | No       |

\*PATCH idempotency depends on implementation

### RESTful API Design with HTTP Methods

```javascript
// Resource: /api/articles

// GET /api/articles - List all articles
app.get("/api/articles", (req, res) => {
  const articles = getAllArticles();
  res.json(articles);
});

// GET /api/articles/:id - Get specific article
app.get("/api/articles/:id", (req, res) => {
  const article = getArticleById(req.params.id);
  res.json(article);
});

// POST /api/articles - Create new article
app.post("/api/articles", (req, res) => {
  const newArticle = createArticle(req.body);
  res.status(201).json(newArticle);
});

// PUT /api/articles/:id - Replace entire article
app.put("/api/articles/:id", (req, res) => {
  const updated = replaceArticle(req.params.id, req.body);
  res.json(updated);
});

// PATCH /api/articles/:id - Update specific fields
app.patch("/api/articles/:id", (req, res) => {
  const updated = updateArticle(req.params.id, req.body);
  res.json(updated);
});

// DELETE /api/articles/:id - Delete article
app.delete("/api/articles/:id", (req, res) => {
  deleteArticle(req.params.id);
  res.status(204).send();
});
```

### Advanced Concepts

#### 1. Content Negotiation

Client and server agree on response format:

```javascript
// Client requests specific format
fetch('https://api.example.com/users/123', {
  headers: {
    'Accept': 'application/json' // or 'application/xml', 'text/html'
  }
});

// Server responds based on Accept header
app.get('/users/:id', (req, res) => {
  const user = getUserById(req.params.id);
  const acceptHeader = req.headers.accept;

  if (acceptHeader.includes('application/json')) {
    res.json(user);
  } else if (acceptHeader.includes('application/xml')) {
    res.type('application/xml');
    res.send(convertToXML(user));
  } else if (acceptHeader.includes('text/html')) {
    res.send(`<html><body><h1>${user.name}</h1></body></html>`);
  } else {
    res.status(406).json({ error: 'Not Acceptable' });
  }
})
```
