# Data Catalog — UNU Master Data API

## Summary

This data catalog documents **all entities and fields** managed by the Master Data API. There are **4 main domains** with **169 fields** that can be selectively accessed by service consumers.

---

## Domain 1: Primary Employee Data

> **Method:** `employee.getPrimary`
> **Description:** Core employee data most frequently accessed by UNU services.
> **Total Fields:** 32

### 1.1 Identification

| # | Field | JSON Key | Type | Required | Description |
|:--|:------|:---------|:-----|:---------|:------------|
| 1 | Employee ID | `id_pegawai` | `string` | ✅ | Primary Key |
| 2 | Status | `status` | `enum` | ✅ | Employment status |
| 3 | Active Status | `status_keaktifan` | `boolean` | ✅ | Active/Inactive |

### 1.2 Basic Information

| # | Field | JSON Key | Type | Required | Description |
|:--|:------|:---------|:-----|:---------|:------------|
| 4 | Email | `email` | `string` | ✅ | Institutional email |
| 5 | NRP | `nrp` | `string` | ✅ | Employee Registration Number |
| 6 | NIDN | `nidn` | `string` | ❌ | Lecturer only, nullable |
| 7 | NIP | `nip` | `string` | ❌ | Civil servant only, nullable |
| 8 | Front Title | `gelar_depan` | `string` | ❌ | Dr., Prof., etc. |
| 9 | Full Name | `nama_lengkap` | `string` | ✅ | — |
| 10 | Back Title | `gelar_belakang` | `string` | ❌ | M.Kom., Ph.D., etc. |
| 11 | Place of Birth | `tempat_lahir` | `string` | ✅ | — |
| 12 | Date of Birth | `tanggal_lahir` | `date` | ✅ | Format: `YYYY-MM-DD` |

### 1.3 Contact

| # | Field | JSON Key | Type | Required | Description |
|:--|:------|:---------|:-----|:---------|:------------|
| 13 | Phone | `telp` | `string` | ✅ | Phone number |
| 14 | WhatsApp | `no_wa` | `string` | ✅ | WhatsApp number |
| 15 | Personal Email | `email_pribadi` | `string` | ✅ | — |
| 16 | Bank Name | `jenis_bank` | `string` | ✅ | Bank name |
| 17 | Account Number | `no_rekening` | `string` | ✅ | — |
| 18 | Account Holder | `atas_nama_rekening` | `string` | ✅ | — |

### 1.4 Basic Employment Info

| # | Field | JSON Key | Type | Required | Description |
|:--|:------|:---------|:-----|:---------|:------------|
| 19 | Join Date | `tanggal_masuk_instansi` | `date` | ✅ | First day at institution |
| 20 | Employee Type | `jenis_pegawai` | `enum` | ✅ | `PNS` / `Non-PNS` |
| 21 | Employee Category | `kategori_pegawai` | `enum` | ✅ | `Dosen` / `Tendik` |
| 22 | Employee Status | `status_pegawai` | `enum` | ✅ | `Tetap` / `Kontrak` / `Honorer` |

### 1.5 Assignment History (Array)

| # | Field | JSON Key | Type | Required | Description |
|:--|:------|:---------|:-----|:---------|:------------|
| 23 | Work Unit | `unit_kerja` | `string/ref` | ✅ | Work unit ID/name |
| 24 | Structural Position | `jabatan_struktural` | `string/ref` | ❌ | Department head, Dean, etc. |
| 25 | Employment Type | `jenis_kepegawaian` | `enum` | ✅ | `Tetap` / `Kontrak` |
| 26 | Start Date | `tanggal_mulai` | `date` | ✅ | Assignment start date |
| 27 | End Date | `tanggal_selesai` | `date` | ❌ | Null if still active |
| 28 | Primary Assignment | `penugasan_utama` | `boolean` | ✅ | `true` = primary |

### 1.6 Education History (Array)

| # | Field | JSON Key | Type | Required | Description |
|:--|:------|:---------|:-----|:---------|:------------|
| 29 | Education Level | `tingkat_pendidikan` | `enum` | ✅ | `SMA`/`D3`/`D4`/`S1`/`S2`/`S3` |
| 30 | Institution Name | `nama_institusi` | `string` | ✅ | School/University |
| 31 | Field of Study | `bidang_studi` | `string` | ✅ | — |
| 32 | Graduation Year | `tahun_lulus` | `integer` | ✅ | — |

---

## Domain 2: Secondary Employee Data

> **Method:** `employee.getSecondary`
> **Description:** Complete employee data including documents, family, and publications.
> **Total Fields:** 122

### 2.1 Basic Identity (15 fields)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 1 | `nrp` | `string` | Primary Key |
| 2 | `nidn` | `string` | Nullable, lecturer only |
| 3 | `nip` | `string` | Nullable, civil servant only |
| 4 | `gelar_depan` | `string` | Nullable |
| 5 | `nama_lengkap` | `string` | — |
| 6 | `gelar_belakang` | `string` | Nullable |
| 7 | `tempat_lahir` | `string` | — |
| 8 | `tanggal_lahir` | `date` | — |
| 9 | `jenis_kelamin` | `enum` | `L` / `P` |
| 10 | `agama` | `string` | — |
| 11 | `status_pernikahan` | `enum` | `single`/`married`/`divorced`/`widowed` |
| 12 | `kewarganegaraan` | `string` | — |
| 13 | `no_sertifikat_pendidik` | `string` | Nullable |
| 14 | `usia_pensiun` | `integer` | — |
| 15 | `aktif` | `boolean` | Active status |

### 2.2 ID Card Address (4 fields)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 16 | `alamat_ktp` | `string` | Full ID card address |
| 17 | `provinsi_ktp` | `string` | — |
| 18 | `kabupaten_ktp` | `string` | — |
| 19 | `kecamatan_ktp` | `string` | — |

### 2.3 Domicile Address (4 fields)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 20 | `alamat_domisili` | `string` | — |
| 21 | `provinsi_domisili` | `string` | — |
| 22 | `kabupaten_domisili` | `string` | — |
| 23 | `kecamatan_domisili` | `string` | — |

### 2.4 Contact (3 fields)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 24 | `telp` | `string` | — |
| 25 | `no_wa` | `string` | — |
| 26 | `email` | `string` | Institutional email |

### 2.5 Physical & Health Data (4 fields)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 27 | `golongan_darah` | `enum` | `A`/`B`/`AB`/`O` |
| 28 | `tinggi_badan` | `integer` | In cm |
| 29 | `berat_badan` | `integer` | In kg |
| 30 | `keterangan_disabilitas` | `string` | Nullable |

### 2.6 Documents & Financial (6 fields)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 31 | `no_ktp` | `string` | National ID number |
| 32 | `no_npwp` | `string` | Nullable |
| 33 | `no_bpjs_tk` | `string` | Employment insurance, nullable |
| 34 | `jenis_bank` | `string` | — |
| 35 | `no_rekening` | `string` | — |
| 36 | `atas_nama_rekening` | `string` | — |

### 2.7 Basic Employment Info (4 fields)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 37 | `tanggal_masuk_instansi` | `date` | — |
| 38 | `jenis_pegawai` | `enum` | `PNS` / `Non-PNS` |
| 39 | `kategori_pegawai` | `enum` | `Dosen` / `Tendik` |
| 40 | `status_pegawai` | `enum` | `Tetap` / `Kontrak` / `Honorer` |

### 2.8 CPNS/PNS Data (4 fields, civil servant only)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 41 | `tmt_cpns` | `date` | Nullable |
| 42 | `no_sk_cpns` | `string` | Nullable |
| 43 | `tmt_pns` | `date` | Nullable |
| 44 | `no_sk_pns` | `string` | Nullable |

### 2.9 Assignment History — Array (10 fields per item)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 45 | `unit_kerja` | `string/ref` | — |
| 46 | `jabatan_struktural` | `string/ref` | Nullable |
| 47 | `jenis_kepegawaian` | `enum` | `Tetap` / `Kontrak` |
| 48 | `tanggal_mulai` | `date` | Assignment start date |
| 49 | `tanggal_selesai` | `date` | Nullable |
| 50 | `penugasan_utama` | `boolean` | — |
| 51 | `nomor_sk` | `string` | For permanent employees |
| 52 | `tanggal_sk` | `date` | For permanent employees |
| 53 | `ditetapkan_oleh` | `string` | For permanent employees |
| 54 | `dokumen_kontrak` | `string(url)` | For contract employees |

### 2.10 Functional Position History — Array (7 fields per item)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 55 | `jabatan_fungsional` | `enum` | Asisten Ahli / Lektor / Lektor Kepala / Guru Besar |
| 56 | `tmt_jabfung` | `date` | — |
| 57 | `no_sk_jabfung` | `string` | — |
| 58 | `tanggal_sk_jabfung` | `date` | — |
| 59 | `ditetapkan_oleh` | `string` | — |
| 60 | `angka_kredit` | `decimal` | Nullable |
| 61 | `dokumen_sk_jabfung` | `string(url)` | Nullable |

### 2.11 Rank/Grade History — Array (7 fields per item)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 62 | `pangkat_golongan` | `string` | III/a, III/b, IV/a, etc. |
| 63 | `tmt_pangkat` | `date` | — |
| 64 | `no_sk_pangkat` | `string` | — |
| 65 | `tanggal_sk_pangkat` | `date` | — |
| 66 | `ditetapkan_oleh` | `string` | — |
| 67 | `peraturan_dasar` | `string` | Nullable |
| 68 | `dokumen_sk_pangkat` | `string(url)` | Nullable |

### 2.12 Education History — Array (8 fields per item)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 69 | `tingkat_pendidikan` | `enum` | `SMA`/`D3`/`D4`/`S1`/`S2`/`S3` |
| 70 | `nama_institusi` | `string` | — |
| 71 | `bidang_studi` | `string` | — |
| 72 | `tahun_lulus` | `integer` | — |
| 73 | `no_ijazah` | `string` | — |
| 74 | `negara` | `string` | — |
| 75 | `kepala_sekolah_dekan` | `string` | Nullable |
| 76 | `dokumen_ijazah` | `string(url)` | Nullable |

### 2.13 Spouse Data (8 fields)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 77 | `nama_pasangan` | `string` | Nullable if unmarried |
| 78 | `tempat_lahir_pasangan` | `string` | — |
| 79 | `tanggal_lahir_pasangan` | `date` | — |
| 80 | `tanggal_nikah` | `date` | — |
| 81 | `no_id_pasangan` | `string` | Spouse national ID |
| 82 | `pekerjaan_pasangan` | `string` | — |
| 83 | `pendidikan_pasangan` | `string` | — |
| 84 | `npwp_pasangan` | `string` | Nullable |

### 2.14 Parent Data — Father (3 fields)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 85 | `nama_ayah` | `string` | — |
| 86 | `no_wa_ayah` | `string` | Nullable |
| 87 | `status_ayah` | `enum` | `Hidup` / `Meninggal` |

### 2.15 Parent Data — Mother (9 fields)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 88 | `nama_ibu` | `string` | — |
| 89 | `tempat_lahir_ibu` | `string` | — |
| 90 | `tanggal_lahir_ibu` | `date` | — |
| 91 | `no_id_ibu` | `string` | National ID |
| 92 | `pekerjaan_ibu` | `string` | — |
| 93 | `pendidikan_ibu` | `string` | — |
| 94 | `npwp_ibu` | `string` | Nullable |
| 95 | `no_wa_ibu` | `string` | Nullable |
| 96 | `status_ibu` | `enum` | `Hidup` / `Meninggal` |

### 2.16 Sibling Data — Array, max 10 (7 fields per item)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 97 | `nama_saudara` | `string` | — |
| 98 | `tempat_lahir_saudara` | `string` | — |
| 99 | `tanggal_lahir_saudara` | `date` | — |
| 100 | `jenis_kelamin_saudara` | `enum` | `L` / `P` |
| 101 | `pekerjaan_saudara` | `string` | — |
| 102 | `pendidikan_saudara` | `string` | — |
| 103 | `no_telp_saudara` | `string` | — |

### 2.17 Children Data — Array, max 10 (10 fields per item)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 104 | `nama_anak` | `string` | — |
| 105 | `tempat_lahir_anak` | `string` | — |
| 106 | `tanggal_lahir_anak` | `date` | — |
| 107 | `urutan_anak` | `integer` | Birth order |
| 108 | `hubungan_anak` | `enum` | `Kandung`/`Tiri`/`Angkat` |
| 109 | `jenis_kelamin_anak` | `enum` | `L` / `P` |
| 110 | `pekerjaan_anak` | `string` | Nullable |
| 111 | `pendidikan_anak` | `string` | — |
| 112 | `status_pernikahan_anak` | `enum` | `single`/`married`/… |
| 113 | `no_telp_anak` | `string` | Nullable |

### 2.18 Publications — Array (9 fields per item)

| # | JSON Key | Type | Description |
|:--|:---------|:-----|:------------|
| 114 | `judul_publikasi` | `string` | — |
| 115 | `jenis_publikasi` | `enum` | Jurnal / Prosiding / Buku |
| 116 | `nama_jurnal_penerbit` | `string` | — |
| 117 | `tahun_terbit` | `integer` | — |
| 118 | `volume` | `string` | Nullable |
| 119 | `halaman` | `string` | Nullable |
| 120 | `doi_url` | `string` | Nullable |
| 121 | `peran` | `enum` | `Penulis Utama` / `Co-Author` |
| 122 | `terindeks` | `string` | Scopus / WoS / Sinta, nullable |

---

## Domain 3: Work Unit Data

> **Method:** `workunit.getAll`, `workunit.getById`
> **Description:** Organizational structure of university work units.
> **Total Fields:** 7

| # | JSON Key | Type | Required | Description |
|:--|:---------|:-----|:---------|:------------|
| 1 | `id` | `string` | ✅ | Primary Key (UUID/ObjectId) |
| 2 | `name` | `string` | ✅ | Work unit name |
| 3 | `code` | `string` | ✅ | Short code (FST, TI, DPMP, etc.) |
| 4 | `is_active` | `boolean` | ✅ | Active status |
| 5 | `parent_id` | `string` | ❌ | Nullable if top level |
| 6 | `is_top_most` | `boolean` | ✅ | `true` if university level |
| 7 | `type` | `enum` | ✅ | `Pendidikan` / `Non-Pendidikan` |

---

## Domain 4: Position Data

> **Method:** `position.getAll`, `position.getByWorkunit`
> **Description:** Position structure per work unit.
> **Total Fields:** 8

| # | JSON Key | Type | Required | Description |
|:--|:---------|:-----|:---------|:------------|
| 1 | `id` | `string` | ✅ | Primary Key (UUID/ObjectId) |
| 2 | `name` | `string` | ✅ | Position name |
| 3 | `code` | `string` | ✅ | Position code |
| 4 | `is_active` | `boolean` | ✅ | Active status |
| 5 | `parent_id` | `string` | ❌ | Nullable if top level |
| 6 | `parent_name` | `string` | ❌ | Denormalized for display |
| 7 | `workunit_id` | `string` | ✅ | Work unit ID |
| 8 | `is_top_most` | `boolean` | ✅ | `true` if highest position |

---

## Standard Enumerations

### Employee Type
```
PNS | Non-PNS
```

### Employee Category
```
Dosen | Tendik
```

### Employee Status
```
Tetap | Kontrak | Honorer
```

### Gender
```
L | P
```

### Marital Status
```
single | married | divorced | widowed
```

### Education Level
```
SMA | D3 | D4 | S1 | S2 | S3
```

### Functional Position
```
Asisten Ahli | Lektor | Lektor Kepala | Guru Besar
```

### Blood Type
```
A | B | AB | O
```

### Work Unit Type
```
Pendidikan | Non-Pendidikan
```
