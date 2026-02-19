# UNU Master Data API

> **Single source of truth** for all employee data across Universitas Nahdlatul Ulama.

[![Go](https://img.shields.io/badge/Go-1.22+-00ADD8?logo=go&logoColor=white)](https://go.dev)
[![Fiber](https://img.shields.io/badge/Fiber-v2-00ACD7?logo=go&logoColor=white)](https://gofiber.io)
[![REST](https://img.shields.io/badge/Protocol-REST%20API-333)](https://restfulapi.net)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+-4169E1?logo=postgresql&logoColor=white)](https://postgresql.org)

---

## 📋 Table of Contents

- [Background](#-background)
- [Tech Stack](#-tech-stack)
- [Architecture](#-architecture)
- [Quick Start](#-quick-start)
- [API Usage](#-api-usage)
- [Documentation](#-documentation)
- [Development Standards](#-development-standards)
- [Project Structure](#-project-structure)

---

## 🎯 Background

### Problem
Currently, each service at UNU (SIOBA, Presensi, Cuti, Anggaran, etc.) accesses employee data directly and **fetches all data** even when only a small subset is needed. This causes:

- ❌ **Data redundancy** — every response contains unnecessary fields
- ❌ **Inconsistency** — data formats differ across services
- ❌ **Logic duplication** — each service writes its own data transformation
- ❌ **Performance** — bandwidth wasted on unused data

### Solution
Master Data API provides **centralized REST endpoints** with **field selection** capability, so each service only receives the data it needs.

```
Before: 15 services × 122 fields = redundant data
After:  15 services × 7-12 fields = efficient (~90% lighter)
```

---

## 🛠 Tech Stack

| Technology | Version | Category | Rationale |
|:-----------|:--------|:---------|:----------|
| **Go** | 1.22+ | Language | High performance, built-in concurrency, single binary |
| **Fiber** | v2.x | HTTP Framework | Built on fasthttp (10x net/http), Express-like API |
| **pgx** | v5.x | DB Driver | Fastest native PostgreSQL driver for Go, connection pooling |
| **zerolog** | latest | Logging | Zero-allocation structured logging, JSON output |
| **validator** | v10 | Validation | Declarative struct validation via tags |
| **PostgreSQL** | 15+ | Database | Reliable RDBMS, JSON support, powerful queries |
| **Docker** | latest | Container | Reproducible builds, easy deployment |
| **Redis** | 7+ | Cache (optional) | Shared cache for multiple instances |

### Go + Fiber

1. **Performance** — Go is a compiled language with efficient garbage collection. Fiber is built on `fasthttp`, providing 10x throughput compared to `net/http`.
2. **Simplicity** — Clean syntax, low learning curve, rich standard library.
3. **Concurrency** — Goroutines and channels make handling thousands of concurrent connections easy.
4. **Single Binary** — Deploy with just one binary file, no runtime dependencies.
5. **Fiber Ecosystem** — Comprehensive middleware: CORS, rate limiter, recover, logger, etc.

### REST API

1. **Industry Standard** — Widely adopted, familiar to all developers.
2. **HTTP Semantics** — Proper use of HTTP methods and status codes.
3. **Cacheable** — GET requests are natively cacheable by browsers, CDNs, and proxies.
4. **Tooling** — Testable via browser, curl, Postman without extra setup.
5. **Field Selection** — Query parameter `?fields=` provides selective data retrieval.

---

## 🏗 Architecture

Uses **Flat DDD** with Bounded Contexts:

```
┌─────────────────────────────────────────────────────────┐
│                    CLIENT SERVICES                       │
│  SIOBA │ Presensi │ Cuti │ Agenda │ Anggaran │ ...      │
└────────┬────────────────────────────────────────────────┘
         │ GET /api/employees/:nrp
         │ GET /api/workunits
         │ GET /api/positions
         ▼
┌─────────────────────────────────────────────────────────┐
│               SHARED KERNEL                              │
│  Fiber HTTP → Router → Middleware (Auth, Rate Limit)     │
└────┬──────────────────┬──────────────────┬─────────────────┘
     ▼                    ▼                    ▼
┌───────────────┐ ┌───────────────┐ ┌───────────────┐
│   employee/   │ │   workunit/   │ │   position/   │
├───────────────┤ ├───────────────┤ ├───────────────┤
│ handler.go    │ │ handler.go    │ │ handler.go    │
│ service.go    │ │ service.go    │ │ service.go    │
│ repo.go       │ │ repo.go       │ │ repo.go       │
│ types.go      │ │ types.go      │ │ types.go      │
│ errors.go     │ │ errors.go     │ │ errors.go     │
└──────┬────────┘ └──────┬────────┘ └──────┬────────┘
       └────────────┴────────────┴────────────┐
                                              ▼
                                     ┌───────────────┐
                                     │  PostgreSQL   │
                                     └───────────────┘
```

> Full details: [docs/architecture.md](docs/architecture.md)

---

## 🚀 Quick Start

### Prerequisites

- Go 1.22+
- PostgreSQL 15+
- Docker & Docker Compose (optional)

### Development

```bash
# Clone repository
git clone <repository-url>
cd master-api

# Copy environment config
cp .env.example .env

# Install dependencies
go mod download

# Run development server
make dev
# or
go run cmd/server/main.go
```

### Docker

```bash
# Build & run
docker-compose up -d

# View logs
docker-compose logs -f app
```

### Environment Variables

| Variable | Default | Description |
|:---------|:--------|:------------|
| `APP_PORT` | `3000` | HTTP server port |
| `APP_ENV` | `development` | Environment (development/production) |
| `DB_HOST` | `localhost` | PostgreSQL host |
| `DB_PORT` | `5432` | PostgreSQL port |
| `DB_NAME` | `master_kepegawaian` | Database name |
| `DB_USER` | `postgres` | Database user |
| `DB_PASSWORD` | — | Database password |
| `DB_MAX_CONNS` | `25` | Max connection pool |
| `CACHE_TTL_MINUTES` | `5` | Cache TTL in minutes |
| `REDIS_URL` | — | Redis URL (optional) |
| `API_KEY` | — | API key for authentication |

---

## 📡 API Usage

### Base URL

```
http://localhost:3000/api
```

### Authentication

```
X-API-Key: <your-api-key>
```

### Example: Get Primary Employee Data

```bash
curl -H "X-API-Key: test-key" \
  "http://localhost:3000/api/employees/123456?fields=nama_lengkap,email,unit_kerja,jabatan_struktural"
```

**Response:**
```json
{
  "success": true,
  "data": {
    "nrp": "123456",
    "nama_lengkap": "Dr. Ahmad Fauzi, M.Kom.",
    "email": "ahmad.fauzi@unu.ac.id",
    "unit_kerja": {
      "id": "uk-001",
      "name": "Fakultas Sains dan Teknologi",
      "code": "FST"
    },
    "jabatan_struktural": "Dekan"
  }
}
```

### Example: List Employees by Work Unit

```bash
curl -H "X-API-Key: test-key" \
  "http://localhost:3000/api/employees?workunit_id=uk-001&fields=nama_lengkap,email"
```

### Available Endpoints

| Method | Endpoint | Description |
|:-------|:---------|:------------|
| `GET` | `/api/employees/:nrp` | Primary employee data |
| `GET` | `/api/employees/:nrp/secondary` | Full secondary data |
| `GET` | `/api/employees` | Search/list employees |
| `GET` | `/api/workunits` | All work units |
| `GET` | `/api/workunits/tree` | Work unit hierarchy |
| `GET` | `/api/workunits/:id` | Work unit details |
| `GET` | `/api/positions` | All positions |
| `GET` | `/api/positions/:id` | Position details |

### Field Selection

Add `?fields=` query parameter to any endpoint:

```
GET /api/employees/123456?fields=nama_lengkap,email,unit_kerja
```

Only the requested fields are returned, reducing response size by ~90%.

### Error Responses

| Status | Description |
|:-------|:------------|
| `200` | Success |
| `400` | Bad request (invalid params) |
| `401` | Unauthorized (missing/invalid API key) |
| `404` | Resource not found |
| `429` | Rate limit exceeded |
| `500` | Internal server error |

```json
{
  "success": false,
  "error": {
    "code": "EMPLOYEE_NOT_FOUND",
    "message": "Employee with NRP 999999 not found"
  }
}
```

---

## 📚 Documentation

| Document | Description |
|:---------|:------------|
| [System Architecture](docs/architecture.md) | DDD, bounded contexts, data flow, deployment |
| [Data Catalog](docs/data-catalog.md) | All entities and fields (169 fields) |
| [Service Catalog](docs/service-catalog.md) | Data mapping per service consumer |
| [Tech Stack](docs/tach-stack.md) | Tech selection rationale & coding standards |

---

## 📏 Development Standards

### Code Style
```bash
# Format code
gofmt -w .

# Lint
golangci-lint run
```

### Naming Convention

| Aspect | Convention | Example |
|:-------|:-----------|:--------|
| Package | lowercase | `employee`, `workunit` |
| Exported type | PascalCase | `EmployeeService` |
| Unexported | camelCase | `parseRequest` |
| JSON key | snake_case | `nama_lengkap` |
| Constant | PascalCase | `MaxConnections` |

### Error Handling
```go
// ✅ Explicit error wrapping
if err != nil {
    return fmt.Errorf("fetch employee by nrp %s: %w", nrp, err)
}

// ❌ Avoid
if err != nil {
    return err // no context
}
```

### Testing
```bash
# Run all tests
make test

# Run with coverage
make test-coverage

# Run specific test
go test ./internal/employee/... -v
```

### Git Convention
```
feat: add employee primary data endpoint
fix: handle null NIDN field
docs: update data catalog
refactor: extract field selector
test: add workunit repository tests
```

---

## 📁 Project Structure

```
master-api/
├── cmd/server/main.go                   # Entry point
├── internal/
│   ├── app/                             # Fiber bootstrap & DI wiring
│   ├── shared/                          # Shared kernel
│   │   ├── config/                      # Configuration
│   │   ├── middleware/                   # Auth, rate limit, logger
│   │   ├── response/                    # Standard JSON response helpers
│   │   └── cache/                       # Cache abstraction
│   ├── employee/                        # Domain (flat DDD)
│   │   ├── handler.go                   # REST endpoint handlers
│   │   ├── service.go                   # Business logic
│   │   ├── repo.go                      # PostgreSQL repository
│   │   ├── types.go                     # Entity, DTOs
│   │   └── errors.go                    # Domain errors
│   ├── workunit/                        # Domain (flat DDD)
│   └── position/                        # Domain (flat DDD)
├── docs/                                # Documentation
├── data/                                # Reference PDFs
├── Makefile
├── Dockerfile
├── docker-compose.yml
└── README.md
```

---

## 📄 License

Internal — Universitas Nahdlatul Ulama
