## Tech Stack Selection & Rationale

| Teknologi | Kategori | Alasan Pemilihan |
|:----------|:---------|:-----------------|
| **Go 1.22+** | Language | Performa tinggi, concurrency primitif, binary tunggal, cocok untuk microservice |
| **Fiber v2** | HTTP Framework | Berbasis fasthttp (10x lebih cepat dari net/http), API intuitif, middleware ecosystem lengkap |
| **pgx v5** | DB Driver | Driver PostgreSQL native Go tercepat, support connection pooling, prepared statements |
| **zerolog** | Logging | Zero-allocation structured logging, JSON output untuk log aggregation |
| **godotenv** | Config | Simple env file loading, twelve-factor app compliant |
| **Docker** | Container | Standar deployment, reproducible builds |
| **PostgreSQL 15+** | Database | Reliable RDBMS, JSON support, rich query capabilities |

---

## Coding Standards

### Project Conventions

| Aspek | Standar |
|:------|:--------|
| **Code style** | `gofmt` + `golangci-lint` |
| **Naming** | PascalCase (exported), camelCase (unexported) |
| **Error handling** | Explicit error return, `fmt.Errorf` with `%w` for wrapping |
| **Testing** | Table-driven tests, `testify` assertions |
| **Git** | Conventional commits, feature branch workflow |
| **Documentation** | GoDoc comments on all exported types/functions |

### JSON Naming Convention

| Go Struct | JSON Key | Alasan |
|:----------|:---------|:-------|
| `NamaLengkap` | `nama_lengkap` | Snake_case konsisten dengan standar JSON Indonesia |
| `UnitKerja` | `unit_kerja` | Mudah dibaca oleh developer |
| `JabatanStruktural` | `jabatan_struktural` | Konsisten dengan dokumen sumber |
