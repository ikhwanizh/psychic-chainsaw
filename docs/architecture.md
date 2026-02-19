# System Architecture — UNU Master Data API

## 1. Overview

UNU Master Data API is the **single source of truth** for all employee data across Universitas Nahdlatul Ulama. This API replaces the redundant data access pattern where each service fetches the entire employee dataset despite only needing a subset of fields.

### Problem Solved

```
┌──────────┐     ┌──────────────┐
│  SIOBA   │────▶│              │  ❌ Each service queries DB directly
│  Presensi│────▶│   Employee   │  ❌ Response contains unnecessary data
│  Cuti    │────▶│   Database   │  ❌ No standardized data format
│  Agenda  │────▶│              │  ❌ Duplicated transformation logic
└──────────┘     └──────────────┘
```

### Solution: Centralized Master API

```
┌──────────┐     ┌───────────────────┐     ┌──────────────┐
│  SIOBA   │────▶│                   │     │              │
│  Presensi│────▶│  Master Data API  │────▶│   Employee   │
│  Cuti    │────▶│   (REST API)      │     │   Database   │
│  Agenda  │────▶│                   │     │              │
└──────────┘     └───────────────────┘     └──────────────┘
                   ✅ Single entry point
                   ✅ Field selection per service
                   ✅ Standardized response format
                   ✅ Centralized caching & logging
```

---

## 2. Application Architecture

### Domain-Driven Design — Flat DDD

Each business domain is organized as a flat package without sub-folders:

```mermaid
graph TB
    subgraph Shared["Shared Kernel"]
        HTTP["HTTP Server (Fiber)"]
        ROUTER["REST Router"]
        MW["Middleware (Auth, Rate Limit, Logger)"]
        CACHE["Cache Layer"]
    end

    subgraph Employee["employee/"]
        E_H["handler.go"]
        E_S["service.go"]
        E_R["repo.go"]
        E_T["types.go"]
    end

    subgraph WorkUnit["workunit/"]
        W_H["handler.go"]
        W_S["service.go"]
        W_R["repo.go"]
    end

    subgraph Position["position/"]
        P_H["handler.go"]
        P_S["service.go"]
        P_R["repo.go"]
    end

    HTTP --> ROUTER
    ROUTER --> MW
    MW --> E_H
    MW --> W_H
    MW --> P_H
    E_H --> E_S
    E_S --> E_R
    W_H --> W_S
    W_S --> W_R
    P_H --> P_S
    P_S --> P_R
    E_R --> CACHE
    W_R --> CACHE
    P_R --> CACHE
```

### File Description (per Domain)

| File | Responsibility |
|:-----|:---------------|
| `handler.go` | REST endpoint handlers, parse path/query params, call service, return JSON response |
| `service.go` | Business logic, use cases, field selection |
| `repo.go` | Database access (PostgreSQL), repository interface + implementation |
| `types.go` | Entity, DTOs, value objects, enums |
| `errors.go` | Domain-specific errors |
| `service_test.go` | Unit tests |
| `mocks/` | Generated mock files |

### Shared Kernel

| Package | Responsibility |
|:--------|:---------------|
| `internal/shared/config/` | Configuration loader |
| `internal/shared/middleware/` | Fiber middleware (auth, rate limit, logger) |
| `internal/shared/response/` | Standard JSON response helpers (success, error) |
| `internal/shared/cache/` | Cache abstraction |
| `internal/app/` | Fiber app bootstrap, DI wiring, route registration |

---

## 3. Protocol: REST API

### Rationale

| # | Reason | Detail |
|:--|:-------|:-------|
| 1 | **Native HTTP Caching** | GET requests are cacheable by browsers, CDNs, and reverse proxies (Nginx) without extra code |
| 2 | **Monitoring & Debugging** | Each endpoint appears separately in access logs and APM tools — no need to parse request bodies |
| 3 | **Tooling Ecosystem** | OpenAPI/Swagger auto-generates docs, client SDKs, and mock servers; endpoints testable via browser |
| 4 | **Granular Rate Limiting** | Rate limits can be set per-endpoint at the infrastructure level without parsing request bodies |
| 5 | **Read-Only API = Perfect GET** | This API is 100% read-only — GET is semantically correct: safe, idempotent, and cacheable |
| 6 | **Horizontal Scalability** | Stateless GET requests are easily load-balanced; CDNs can cache popular endpoints at edge |

### Endpoint Design

| Method | Endpoint | Description |
|:-------|:---------|:------------|
| `GET` | `/api/employees/:nrp` | Primary employee data |
| `GET` | `/api/employees/:nrp/secondary` | Full secondary data |
| `GET` | `/api/employees` | Search/list employees |
| `GET` | `/api/employees?workunit_id=x` | Employees by work unit |
| `GET` | `/api/workunits` | All work units |
| `GET` | `/api/workunits/tree` | Work unit hierarchy |
| `GET` | `/api/workunits/:id` | Work unit details |
| `GET` | `/api/positions` | All positions |
| `GET` | `/api/positions?workunit_id=x` | Positions by work unit |
| `GET` | `/api/positions/:id` | Position details |

### Field Selection

All endpoints support the `?fields=` query parameter to request only specific fields:

```
GET /api/employees/123456?fields=nama_lengkap,email,unit_kerja
```

Without `?fields=`, all available fields are returned.

### Success Response Format

```json
{
  "success": true,
  "data": {
    "nrp": "123456",
    "nama_lengkap": "Dr. Ahmad Fauzi, M.Kom.",
    "email": "ahmad.fauzi@unu.ac.id"
  }
}
```

### Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "EMPLOYEE_NOT_FOUND",
    "message": "Employee with NRP 999999 not found"
  }
}
```

### HTTP Status Codes

| Status | Usage |
|:-------|:------|
| `200 OK` | Successful request |
| `400 Bad Request` | Invalid parameters or query |
| `401 Unauthorized` | Missing or invalid API key |
| `404 Not Found` | Resource not found |
| `429 Too Many Requests` | Rate limit exceeded |
| `500 Internal Server Error` | Unexpected server error |

---

## 4. Project Folder Structure

```
master-api/
├── cmd/
│   └── server/
│       └── main.go                      # Entry point
├── internal/
│   ├── app/
│   │   ├── app.go                       # Fiber app setup, DI wiring
│   │   └── routes.go                    # Register all domain routes
│   ├── shared/                          # Shared Kernel
│   │   ├── config/
│   │   │   └── config.go                # Configuration loader
│   │   ├── middleware/
│   │   │   ├── auth.go                  # API Key authentication
│   │   │   ├── ratelimit.go             # Per-service rate limiting
│   │   │   └── logger.go               # Request/response logging
│   │   ├── response/
│   │   │   └── response.go             # Standard JSON response helpers
│   │   └── cache/
│   │       └── cache.go                 # Cache abstraction
│   ├── employee/                        # Domain: Employee
│   │   ├── mocks/                       # Generated mocks
│   │   ├── handler.go                   # REST endpoint handlers
│   │   ├── service.go                   # Business logic + field selection
│   │   ├── repo.go                      # PostgreSQL repository
│   │   ├── types.go                     # Entity, DTOs, value objects
│   │   ├── errors.go                    # Domain-specific errors
│   │   └── service_test.go              # Unit tests
│   ├── workunit/                        # Domain: Work Unit
│   │   ├── handler.go
│   │   ├── service.go
│   │   ├── repo.go
│   │   ├── types.go
│   │   └── errors.go
│   └── position/                        # Domain: Position
│       ├── handler.go
│       ├── service.go
│       ├── repo.go
│       ├── types.go
│       └── errors.go
├── docs/                                # Documentation
│   ├── architecture.md
│   ├── data-catalog.md
│   ├── service-catalog.md
│   └── tach-stack.md
├── data/                                # Reference PDFs
├── go.mod
├── go.sum
├── Makefile
├── Dockerfile
├── docker-compose.yml
└── README.md
```

---

## 5. Data Flow

### Request Flow

```mermaid
sequenceDiagram
    participant C as Client Service
    participant F as Fiber HTTP
    participant R as Router
    participant M as Middleware
    participant H as Handler
    participant S as Service
    participant CA as Cache
    participant DB as Database

    C->>F: GET /api/employees/123456?fields=nama_lengkap,email
    F->>R: Route to employee handler
    R->>M: Auth + Rate Limit + Logging
    M->>H: Call handler
    H->>H: Parse path params & query params
    H->>S: Call service method
    S->>CA: Check cache
    alt Cache hit
        CA-->>S: Return cached data
    else Cache miss
        S->>DB: Query database
        DB-->>S: Raw data
        S->>S: Apply field selection
        S->>CA: Store in cache
    end
    S-->>H: Filtered result
    H-->>C: HTTP 200 + JSON response
```

---

## 6. Caching Strategy

| Level | Mechanism | TTL | Description |
|:------|:----------|:----|:------------|
| L1 | In-memory (sync.Map) | 5 min | Hot data, per-instance |
| L2 | Redis (optional) | 15 min | Shared cache across instances |

### Cache Key Pattern
```
master-api:{endpoint}:{hash(params)}
```

---

## 7. Security

| Aspect | Implementation |
|:-------|:---------------|
| **Authentication** | API Key via `X-API-Key` header |
| **Rate Limiting** | Per-service throttling via Fiber middleware |
| **Input Validation** | Query/path parameter validation |
| **CORS** | Configurable allowed origins |
| **Logging** | Structured logging (zerolog) |

---

## 8. Deployment

```mermaid
graph LR
    subgraph Production
        LB["Load Balancer"]
        A1["Instance 1"]
        A2["Instance 2"]
        DB[(PostgreSQL)]
        RD[(Redis)]
    end

    LB --> A1
    LB --> A2
    A1 --> DB
    A2 --> DB
    A1 --> RD
    A2 --> RD
```

- **Containerization:** Docker + Docker Compose
- **Horizontal scaling:** Stateless instances behind load balancer
- **Health check:** `GET /health` endpoint
- **Graceful shutdown:** Signal handling for zero-downtime deploy
