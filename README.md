# UNU Master Data API

> **Single source of truth** for all employee data across Universitas Nahdlatul Ulama.

[![Go](https://img.shields.io/badge/Go-1.22+-00ADD8?logo=go&logoColor=white)](https://go.dev)
[![Fiber](https://img.shields.io/badge/Fiber-v2-00ACD7?logo=go&logoColor=white)](https://gofiber.io)
[![JSON-RPC](https://img.shields.io/badge/Protocol-JSON--RPC%202.0-333)](https://www.jsonrpc.org/specification)
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
Master Data API provides a **single centralized endpoint** using **JSON-RPC 2.0** with **field selection** capability, so each service only receives the data it needs.

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

### JSON-RPC 2.0

1. **Single Endpoint** — All methods through `POST /rpc`, simple routing.
2. **Batch Support** — Send multiple requests in a single HTTP call.
3. **Explicit Methods** — Clear method naming: `employee.getPrimary`.
4. **Standard Errors** — Standardized error codes.
5. **Field Selection** — Params object naturally supports a `fields` parameter.

---

## 🏗 Architecture

Uses **Flat DDD** with Bounded Contexts:

```
┌─────────────────────────────────────────────────────────┐
│                    CLIENT SERVICES                       │
│  SIOBA │ Presensi │ Cuti │ Agenda │ Anggaran │ ...      │
└────────┬────────────────────────────────────────────────┘
         │ POST /rpc (JSON-RPC 2.0)
         ▼
┌─────────────────────────────────────────────────────────┐
│               SHARED KERNEL                              │
│  Fiber HTTP → JSON-RPC Dispatcher → Middleware            │
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

### Endpoint

```
POST /rpc
Content-Type: application/json
X-API-Key: <your-api-key>
```

### Example: Get Primary Employee Data

**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "employee.getPrimary",
  "params": {
    "nrp": "123456",
    "fields": ["nama_lengkap", "email", "unit_kerja", "jabatan_struktural"]
  },
  "id": 1
}
```

**Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {
    "nrp": "123456",
    "nama_lengkap": "Dr. Ahmad Fauzi, M.Kom.",
    "email": "ahmad.fauzi@unu.ac.id",
    "unit_kerja": {
      "id": "uk-001",
      "name": "Fakultas Sains dan Teknologi",
      "code": "FST"
    },
    "jabatan_struktural": "Dekan"
  },
  "id": 1
}
```

### Example: Batch Request

```json
[
  {
    "jsonrpc": "2.0",
    "method": "employee.getPrimary",
    "params": { "nrp": "123456", "fields": ["nama_lengkap", "email"] },
    "id": 1
  },
  {
    "jsonrpc": "2.0",
    "method": "workunit.getAll",
    "params": { "fields": ["name", "code"] },
    "id": 2
  }
]
```

### Available Methods

| Method | Description |
|:-------|:------------|
| `employee.getPrimary` | Primary employee data |
| `employee.getSecondary` | Secondary employee data (full) |
| `employee.search` | Search employees |
| `employee.getByWorkunit` | Employees by work unit |
| `workunit.getAll` | All work units |
| `workunit.getById` | Work unit details |
| `workunit.getTree` | Work unit hierarchy |
| `position.getAll` | All positions |
| `position.getByWorkunit` | Positions by work unit |
| `position.getById` | Position details |

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
| Package | lowercase | `service`, `repository` |
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
feat: add employee primary data method
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
│   │   ├── jsonrpc/                     # JSON-RPC 2.0 core
│   │   └── cache/                       # Cache abstraction
│   ├── employee/                        # Domain (flat DDD)
│   │   ├── handler.go                   # JSON-RPC method handlers
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
