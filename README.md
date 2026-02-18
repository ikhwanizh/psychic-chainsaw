# UNU Master Data API

> **Single source of truth** untuk seluruh data kepegawaian di lingkungan Universitas Nahdlatul Ulama.

[![Go](https://img.shields.io/badge/Go-1.22+-00ADD8?logo=go&logoColor=white)](https://go.dev)
[![Fiber](https://img.shields.io/badge/Fiber-v2-00ACD7?logo=go&logoColor=white)](https://gofiber.io)
[![JSON-RPC](https://img.shields.io/badge/Protocol-JSON--RPC%202.0-333)](https://www.jsonrpc.org/specification)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+-4169E1?logo=postgresql&logoColor=white)](https://postgresql.org)

---

## 📋 Daftar Isi

- [Latar Belakang](#-latar-belakang)
- [Tech Stack](#-tech-stack)
- [Arsitektur](#-arsitektur)
- [Quick Start](#-quick-start)
- [API Usage](#-api-usage)
- [Dokumentasi](#-dokumentasi)
- [Standar Pengembangan](#-standar-pengembangan)
- [Struktur Proyek](#-struktur-proyek)

---

## 🎯 Latar Belakang

### Masalah
Saat ini, setiap service di UNU (SIOBA, Presensi, Cuti, Anggaran, dll.) mengakses data kepegawaian secara langsung dan **mengambil seluruh data** meskipun hanya membutuhkan sebagian kecil. Ini menyebabkan:

- ❌ **Redundansi data** — setiap response berisi field yang tidak diperlukan
- ❌ **Inkonsistensi** — format data berbeda antar service
- ❌ **Duplikasi logic** — setiap service menulis transformasi datanya sendiri
- ❌ **Performance** — bandwidth terbuang untuk data yang tidak dipakai

### Solusi
Master Data API menyediakan **satu endpoint terpusat** menggunakan **JSON-RPC 2.0** dengan kemampuan **field selection**, sehingga setiap service hanya menerima data yang dibutuhkan.

```
Sebelum: 15 service × 122 field = data redundant
Sesudah: 15 service × 7-12 field = efisien (~90% lebih ringan)
```

---

## 🛠 Tech Stack

| Teknologi | Versi | Kategori | Alasan |
|:----------|:------|:---------|:-------|
| **Go** | 1.22+ | Language | Performa tinggi, concurrency built-in, binary tunggal |
| **Fiber** | v2.x | HTTP Framework | Berbasis fasthttp (10x net/http), API Express-like |
| **pgx** | v5.x | DB Driver | Native PostgreSQL driver Go tercepat, connection pooling |
| **zerolog** | latest | Logging | Zero-allocation structured logging, JSON output |
| **validator** | v10 | Validation | Declarative struct validation via tags |
| **PostgreSQL** | 15+ | Database | RDBMS andal, JSON support, query power |
| **Docker** | latest | Container | Reproducible builds, easy deployment |
| **Redis** | 7+ | Cache (opsional) | Shared cache untuk multiple instances |

### Mengapa Go + Fiber?

1. **Performance** — Go compiled language dengan garbage collection yang efisien. Fiber berbasis `fasthttp` yang memberikan throughput 10x lipat dibanding `net/http`.
2. **Simplicity** — Syntax sederhana, learning curve rendah, stdlib yang kaya.
3. **Concurrency** — Goroutines dan channels memudahkan handle thousands of concurrent connections.
4. **Single Binary** — Deploy cukup satu binary file, tanpa dependency runtime.
5. **Fiber Ecosystem** — Middleware yang lengkap: CORS, rate limiter, recover, logger, dll.

### Mengapa JSON-RPC 2.0?

1. **Single Endpoint** — Semua method melalui `POST /rpc`, sederhana.
2. **Batch Support** — Kirim multiple request dalam satu HTTP call.
3. **Explicit Method** — Method naming yang jelas: `employee.getPrimary`.
4. **Error Standard** — Error codes yang terstandarisasi.
5. **Field Selection** — Params object memungkinkan `fields` parameter secara natural.

---

## 🏗 Arsitektur

Menggunakan **Flat DDD** (terinspirasi pattern `go-moneter`):

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

> Detail lengkap: [docs/architecture.md](docs/architecture.md)

---

## 🚀 Quick Start

### Prerequisites

- Go 1.22+
- PostgreSQL 15+
- Docker & Docker Compose (opsional)

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
# atau
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

| Variable | Default | Deskripsi |
|:---------|:--------|:----------|
| `APP_PORT` | `3000` | Port HTTP server |
| `APP_ENV` | `development` | Environment (development/production) |
| `DB_HOST` | `localhost` | PostgreSQL host |
| `DB_PORT` | `5432` | PostgreSQL port |
| `DB_NAME` | `master_kepegawaian` | Database name |
| `DB_USER` | `postgres` | Database user |
| `DB_PASSWORD` | — | Database password |
| `DB_MAX_CONNS` | `25` | Max connection pool |
| `CACHE_TTL_MINUTES` | `5` | Cache TTL in minutes |
| `REDIS_URL` | — | Redis URL (opsional) |
| `API_KEY` | — | API key untuk autentikasi |

---

## 📡 API Usage

### Endpoint

```
POST /rpc
Content-Type: application/json
X-API-Key: <your-api-key>
```

### Contoh: Ambil Data Primer Pegawai

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

### Contoh: Batch Request

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

| Method | Deskripsi |
|:-------|:----------|
| `employee.getPrimary` | Data kepegawaian primer |
| `employee.getSecondary` | Data kepegawaian sekunder (lengkap) |
| `employee.search` | Cari pegawai |
| `employee.getByWorkunit` | Pegawai per unit kerja |
| `workunit.getAll` | Semua unit kerja |
| `workunit.getById` | Detail unit kerja |
| `workunit.getTree` | Hierarki unit kerja |
| `position.getAll` | Semua jabatan |
| `position.getByWorkunit` | Jabatan per unit kerja |
| `position.getById` | Detail jabatan |

---

## 📚 Dokumentasi

| Dokumen | Deskripsi |
|:--------|:----------|
| [Arsitektur Sistem](docs/architecture.md) | DDD, bounded contexts, data flow, deployment |
| [Data Catalog](docs/data-catalog.md) | Seluruh entitas dan field (169 fields) |
| [Service Catalog](docs/service-catalog.md) | Pemetaan data per service consumer |
| [Development Plan](docs/development-plan.md) | Sprint plan, tech rationale, estimasi |

---

## 📏 Standar Pengembangan

### Code Style
```bash
# Format code
gofmt -w .

# Lint
golangci-lint run
```

### Naming Convention

| Aspek | Konvensi | Contoh |
|:------|:---------|:-------|
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

// ❌ Hindari
if err != nil {
    return err // tanpa konteks
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

## 📁 Struktur Proyek

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
