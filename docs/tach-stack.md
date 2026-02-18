## Tech Stack Selection & Rationale

| Technology | Category | Rationale |
|:-----------|:---------|:----------|
| **Go 1.22+** | Language | High performance, concurrency primitives, single binary, ideal for microservices |
| **Fiber v2** | HTTP Framework | Built on fasthttp (10x faster than net/http), intuitive API, comprehensive middleware ecosystem |
| **pgx v5** | DB Driver | Fastest native PostgreSQL driver for Go, connection pooling support, prepared statements |
| **zerolog** | Logging | Zero-allocation structured logging, JSON output for log aggregation |
| **godotenv** | Config | Simple env file loading, twelve-factor app compliant |
| **Docker** | Container | Standard deployment, reproducible builds |
| **PostgreSQL 15+** | Database | Reliable RDBMS, JSON support, rich query capabilities |

---

## Coding Standards

### Project Conventions

| Aspect | Standard |
|:-------|:---------|
| **Code style** | `gofmt` + `golangci-lint` |
| **Naming** | PascalCase (exported), camelCase (unexported) |
| **Error handling** | Explicit error return, `fmt.Errorf` with `%w` for wrapping |
| **Testing** | Table-driven tests, `testify` assertions |
| **Git** | Conventional commits, feature branch workflow |
| **Documentation** | GoDoc comments on all exported types/functions |

### JSON Naming Convention

| Go Struct | JSON Key | Rationale |
|:----------|:---------|:----------|
| `NamaLengkap` | `nama_lengkap` | Snake_case consistent with Indonesian JSON standards |
| `UnitKerja` | `unit_kerja` | Easy to read for developers |
| `JabatanStruktural` | `jabatan_struktural` | Consistent with source documents |
