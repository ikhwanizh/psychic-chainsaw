# Arsitektur Sistem — UNU Master Data API

## 1. Gambaran Umum

UNU Master Data API adalah **single source of truth** untuk seluruh data kepegawaian di lingkungan Universitas Nahdlatul Ulama. API ini menggantikan pola akses data yang redundant di mana setiap service mengambil seluruh data pegawai meskipun hanya membutuhkan sebagian field.

### Masalah yang Diselesaikan

```
┌──────────┐     ┌──────────────┐
│  SIOBA   │────▶│              │  ❌ Setiap service query langsung ke DB
│  Presensi│────▶│   Database   │  ❌ Response berisi data yang tidak diperlukan
│  Cuti    │────▶│  Kepegawaian │  ❌ Tidak ada standarisasi format data
│  Agenda  │────▶│              │  ❌ Duplikasi logic transformasi data
└──────────┘     └──────────────┘
```

### Solusi: Centralized Master API

```
┌──────────┐     ┌───────────────────┐     ┌──────────────┐
│  SIOBA   │────▶│                   │     │              │
│  Presensi│────▶│  Master Data API  │────▶│   Database   │
│  Cuti    │────▶│  (JSON-RPC 2.0)   │     │  Kepegawaian │
│  Agenda  │────▶│                   │     │              │
└──────────┘     └───────────────────┘     └──────────────┘
                   ✅ Single entry point
                   ✅ Field selection per service
                   ✅ Standar format response
                   ✅ Centralized caching & logging
```

---

## 2. Arsitektur Aplikasi

### Domain-Driven Design — DDD

Menggunakan pendekatan **DDD** setiap domain bisnis diorganisasi sebagai flat package tanpa sub-folder:

```mermaid
graph TB
    subgraph Shared["Shared Kernel"]
        HTTP["HTTP Server (Fiber)"]
        RPC["JSON-RPC 2.0 Dispatcher"]
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

    HTTP --> RPC
    RPC --> MW
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

| File | Tanggung Jawab |
|:-----|:---------------|
| `handler.go` | JSON-RPC method handler, parse params, call service, return response |
| `service.go` | Business logic, use cases, field selection |
| `repo.go` | Database access (PostgreSQL), repository interface + implementation |
| `types.go` | Entity, DTOs, value objects, enums |
| `errors.go` | Domain-specific errors |
| `service_test.go` | Unit tests |
| `mocks/` | Generated mock files |

### Shared Kernel

| Package | Tanggung Jawab |
|:--------|:---------------|
| `internal/shared/config/` | Configuration loader |
| `internal/shared/middleware/` | Fiber middleware (auth, rate limit, logger) |
| `internal/shared/jsonrpc/` | JSON-RPC 2.0 request/response, dispatcher, errors |
| `internal/shared/cache/` | Cache abstraction |
| `internal/app/` | Fiber app bootstrap, DI wiring, route registration |

---

## 3. Protokol: JSON-RPC 2.0

### Mengapa JSON-RPC?

| Aspek | REST | JSON-RPC 2.0 | Pilihan |
|:------|:-----|:-------------|:--------|
| Endpoint | Banyak URL path | Single endpoint `/rpc` | ✅ JSON-RPC |
| Batch request | Tidak standar | Built-in | ✅ JSON-RPC |
| Method naming | HTTP verbs + URL | Explicit method name | ✅ JSON-RPC |
| Field selection | Query params (ad-hoc) | Params object (standar) | ✅ JSON-RPC |
| Dokumentasi | OpenAPI/Swagger | Self-describing methods | ✅ JSON-RPC |

### Format Request

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

### Format Response

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

---

## 4. Struktur Folder Proyek

```
master-api/
├── cmd/
│   └── server/
│       └── main.go                      # Entry point
├── internal/
│   ├── app/
│   │   ├── app.go                       # Fiber app setup, DI wiring
│   │   └── routes.go                    # Register semua domain handlers
│   ├── shared/                          # Shared Kernel
│   │   ├── config/
│   │   │   └── config.go                # Configuration loader
│   │   ├── middleware/
│   │   │   ├── auth.go                  # API Key authentication
│   │   │   ├── ratelimit.go             # Per-service rate limiting
│   │   │   └── logger.go               # Request/response logging
│   │   ├── jsonrpc/
│   │   │   ├── request.go               # JSON-RPC 2.0 request types
│   │   │   ├── response.go              # JSON-RPC 2.0 response types
│   │   │   ├── dispatcher.go            # Method router & dispatcher
│   │   │   └── errors.go               # Standard error codes
│   │   └── cache/
│   │       └── cache.go                 # Cache abstraction
│   ├── employee/                        # Domain: Employee
│   │   ├── mocks/                       # Generated mocks
│   │   ├── handler.go                   # JSON-RPC method handlers
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
│   └── development-plan.md
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
    participant R as JSON-RPC Router
    participant M as Middleware
    participant S as Service
    participant CA as Cache
    participant DB as Database

    C->>F: POST /rpc (JSON-RPC Request)
    F->>R: Parse & Route by method
    R->>M: Auth + Rate Limit + Logging
    M->>S: Call service method
    S->>CA: Check cache
    alt Cache hit
        CA-->>S: Return cached data
    else Cache miss
        S->>DB: Query database
        DB-->>S: Raw data
        S->>S: Apply field selection
        S->>CA: Store in cache
    end
    S-->>M: Filtered response
    M-->>R: Response
    R-->>F: JSON-RPC Response
    F-->>C: HTTP 200 + JSON body
```

---

## 6. Strategi Caching

| Level | Mekanisme | TTL | Keterangan |
|:------|:----------|:----|:-----------|
| L1 | In-memory (sync.Map) | 5 min | Hot data, per-instance |
| L2 | Redis (opsional) | 15 min | Shared cache antar instance |

### Cache Key Pattern
```
master-api:{method}:{hash(params)}
```

---

## 7. Security

| Aspek | Implementasi |
|:------|:-------------|
| **Authentication** | API Key via `X-API-Key` header |
| **Rate Limiting** | Per-service throttling via Fiber middleware |
| **Input Validation** | JSON-RPC params validation |
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
