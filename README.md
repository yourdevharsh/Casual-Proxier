```md
# 🔗 Casual API Proxier: Human-Friendly API Endpoint Generator

Casual API Proxier is a lightweight backend system that converts **complex third-party API endpoints into simple, human-friendly routes**. Instead of remembering complicated query parameters and versioned paths, developers can create readable endpoints like `/getweatherofnewyork` or `/nyweather` that internally proxy requests to the original API.

---

# 🌟 Intro

Modern APIs often expose **long, complex endpoints** that require multiple parameters, authentication headers, and versioned paths.

Example:

```

/weatherapp/api/v3?city=newyork&units=metric&apikey=...

```

These endpoints can be:

- Hard to remember  
- Difficult to reuse  
- Error-prone when manually constructing requests  

**Casual API Proxier** solves this problem by allowing developers to:

1. Provide an API website URL  
2. Add required API keys  
3. Define custom query mappings  

The system then **generates simple, readable endpoints** that internally proxy requests to the original API.

Example transformation:

```

Complex API:
[https://weatherapi.com/api/v3?city=newyork](https://weatherapi.com/api/v3?city=newyork)

Human Friendly Endpoint:
GET /getweatherofnewyork
GET /nyweather

```

This makes APIs easier to **integrate, remember, and share across projects.**

---

# 🛠️ How It Works

The application follows a **secure proxy architecture** where requests are authenticated, mapped, and forwarded to external APIs.

### 1. Client Request

A developer calls a human-friendly endpoint:

```

GET /nyweather

```

or

```

GET /getweatherofnewyork

```

The request contains a **JWT token for authentication**.

---

### 2. Authentication Layer (JWT)

The request first passes through a middleware that validates the JWT token.

```

Authorization: Bearer <token>

```

The middleware verifies the token and extracts the **userId**.

If valid:

```

req.user = jwt.verify(token, SECRET_KEY)

```

Otherwise the request is rejected.

---

### 3. Express Routing

After authentication, the system routes the request to the user-specific proxy configuration.

Example route:

```

GET /:userid

```

This endpoint retrieves API mappings stored for that user.

---

### 4. Database Lookup

The server queries the database to fetch stored API mappings associated with the user.

```

SELECT * FROM smartapi WHERE userId = $1

```

These mappings define:

- original API endpoint  
- parameters  
- friendly route aliases  

The database is managed through a PostgreSQL connection pool.

---

### 5.
```
