# 🔗 Casual API Proxier: Human-Friendly API Endpoint Generator

Casual API Proxier is a lightweight backend system that converts **complex third-party API endpoints into simple, human-friendly routes**. Instead of remembering complicated query parameters and versioned paths, developers can create readable endpoints like `/getweatherofnewyork` or `/nyweather` that internally proxy requests to the original API.

---

## 🌟 Intro

Modern APIs often expose **long, complex endpoints** that require multiple parameters, authentication headers, and versioned paths.

**Example:**
```text
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

**Example transformation:**
*Complex API:*
```text
[https://weatherapi.com/api/v3?city=newyork](https://weatherapi.com/api/v3?city=newyork)
```
*Human Friendly Endpoints:*
```text
GET /getweatherofnewyork
GET /nyweather
```

This makes APIs easier to **integrate, remember, and share across projects.**

---

## 🛠️ How It Works

The application follows a **secure proxy architecture** where requests are authenticated, mapped, and forwarded to external APIs.

### 1. Client Request
A developer calls a human-friendly endpoint:
```http
GET /nyweather
```
or
```http
GET /getweatherofnewyork
```
The request contains a **JWT token for authentication**.

### 2. Authentication Layer (JWT)
The request first passes through a middleware that validates the JWT token.
```http
Authorization: Bearer <token>
```
The middleware verifies the token and extracts the **userId**. If valid:
```javascript
req.user = jwt.verify(token, SECRET_KEY)
```
Otherwise, the request is rejected.

### 3. Express Routing
After authentication, the system routes the request to the user-specific proxy configuration.

Example route:
```javascript
GET /:userid
```
This endpoint retrieves API mappings stored for that user.

### 4. Database Lookup
The server queries the database to fetch stored API mappings associated with the user.
```sql
SELECT * FROM smartapi WHERE userId = $1
```
These mappings define:
- Original API endpoint
- Parameters
- Friendly route aliases

The database is managed through a PostgreSQL connection pool.

### 5. Proxy Execution
Once the mapping is retrieved:
1. The friendly endpoint is matched.
2. Parameters are inserted.
3. The request is forwarded to the external API.
4. The response is returned to the client.

*Example internal transformation:*
`/nyweather` **↓** `/weatherapp/api/v3?city=newyork`

---

## ✨ Features

- **Human-Friendly API Endpoints**
- **Automatic API Proxy Routing**
- **JWT-Protected API Access**
- **Multi-User API Mapping Support**
- **Database-Driven Endpoint Configuration**
- **Custom Query Parameter Mapping**
- **Secure Middleware Authentication**
- **Centralized API Management**
- **Lightweight Node.js Server**

---

## 💻 Tech Stack

| Component | Technology | Role |
|---|---|---|
| **Backend** | Node.js / Express | REST API routing and proxy handling |
| **Authentication** | JSON Web Token (JWT) | Secure request verification |
| **Database** | PostgreSQL | Stores user API mappings |
| **DB Driver** | pg (node-postgres) | Database connection pooling |
| **Environment Config** | dotenv | Secure secret management |
| **Middleware** | Express Middleware | Request validation and routing |

---

## 🚀 Getting Started

### Prerequisites
- Node.js (LTS recommended)
- PostgreSQL database
- API keys for the external services you want to proxy

### Setup and Installation

1. **Clone the repository:**
   ```bash
   git clone <your-repo-url>
   cd casual-api-proxier
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Configure Environment:**
   Create a `.env` file in the root directory:
   ```env
   SECRET_KEY=your_jwt_secret
   PG_PASSWORD=your_postgres_password
   ```

4. **Run the Server:**
   ```bash
   node server.js
   ```
   *Server starts at: `http://localhost:3000`*

---

## 📐 System Design and Architecture

### Data Flow
The architecture follows a secure proxy gateway design:

```text
Client Request
      ↓
JWT Authentication
      ↓
Express Routing
      ↓
Database Lookup
      ↓
API Mapping Engine
      ↓
External API Call
      ↓
Response Returned
```

### Core Components

* **`server.js`**
  Main application server responsible for Express routing, middleware integration, and request orchestration.
* **`checkJwt.js`**
  Authentication middleware that verifies JWT tokens, handles token extraction, signature verification, and expiration handling.
* **`sendUserData.js`**
  Handles retrieval of API mapping data from the database, fetches configurations, and prepares proxy data.
* **`db.js`**
  Database connection module using PostgreSQL connection pooling to manage connections and prevent exhaustion.

---

## 📊 DSA Analysis and Potential Improvements

### Endpoint Mapping
**Data Structure:** Array / Map of API routes
```json
{
  "/nyweather": "/weatherapi/v3?city=newyork"
}
```
**Lookup Complexity:** 1. Using a hash map ensures extremely fast route resolution.

### Request Routing
**Algorithm:** Route Matching → Parameter Injection → API Proxy
**Time Complexity:** 1. Express handles route resolution efficiently.

### Database Lookup
**Query Complexity:** log N when indexed by `userId`.
**Potential Optimization (API Mapping Cache):** Instead of querying the database on every request, cache mappings in Redis and refresh periodically. This would reduce database load significantly.

---

## 📈 Performance Metrics

| Metric | Description | Expected Value |
|---|---|---|
| **Proxy Latency** | Time to forward API request | ~50–200ms overhead |
| **Authentication Time** | JWT verification time | <5ms |
| **Route Resolution** | Express route matching | Near constant time |
| **DB Query Time** | Fetch user API mappings | ~10–30ms |
| **API Throughput** | Requests per second | Dependent on external API |

---

## ⚖️ Trade-offs: Why Use This Architecture?

| Trade-off | Rationale |
|---|---|
| **Proxy Layer vs Direct API** | Adds slight latency but enables friendly endpoints and centralized management. |
| **JWT Authentication** | Ensures secure access to API mappings without session overhead. |
| **Database Mapping** | Allows persistent user-defined endpoints across restarts. |
| **Express Server** | Lightweight and fast for proxy workloads. |
| **PostgreSQL** | Strong relational structure for user/API relationships. |

---

## 🚀 Real Use Cases

Casual API Proxier can be used for:

* **API Simplification:** Convert `/api/v2/weather?city=london&units=metric` into `/londonweather`.
* **Developer Tooling:** Create easy internal APIs for microservices, dashboards, and automation scripts.
* **API Aggregation:** Combine multiple APIs behind friendly endpoints like `/crypto`, `/stocks`, `/weather`, or `/news`.

---

## 🔮 Future Updates

- **Smart Endpoint Generation:** Automatically generate endpoint names from API documentation.
- **OpenAPI Integration:** Allow importing APIs directly from Swagger / OpenAPI specs.
- **Rate Limiting:** Add `express-rate-limit` to protect APIs from abuse.
- **Response Caching:** Cache responses using Redis to reduce API costs and improve latency.
- **Web Dashboard:** Provide a UI for creating API proxies, editing mappings, and managing endpoints.
- **API Analytics:** Track request counts, most used endpoints, and latency metrics.

---

## 📌 Example Usage

**Register API mapping:**
*Original API:* `/weatherapi/v3?city=newyork`
*Friendly endpoint:* `GET /nyweather`

**Client request:**
```bash
curl http://localhost:3000/nyweather
```

**Response:**
```json
{
  "temperature": "15°C",
  "condition": "Cloudy"
}
```

---

## 🧠 Project Vision

Casual API Proxier aims to become a developer-friendly API abstraction layer that allows engineers to simplify third-party APIs, build readable endpoints, create reusable integrations, and reduce complexity in application development.

---

## 📜 License

MIT License

⭐ **If you found this project useful, consider giving it a star!**
