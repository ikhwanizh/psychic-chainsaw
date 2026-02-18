# Data Catalog — UNU Master Data API

## Ringkasan

Data catalog ini mendokumentasikan **seluruh entitas dan field** yang dikelola oleh Master Data API. Total terdapat **4 domain utama** dengan **169 field** yang dapat diakses secara selektif oleh service consumer.

---

## Domain 1: Data Kepegawaian Primer

> **Method:** `employee.getPrimary`
> **Deskripsi:** Data dasar pegawai yang paling sering diakses oleh service-service UNU.
> **Total Fields:** 32

### 1.1 Identifikasi

| # | Field | JSON Key | Type | Required | Keterangan |
|:--|:------|:---------|:-----|:---------|:-----------|
| 1 | ID Pegawai | `id_pegawai` | `string` | ✅ | Primary Key |
| 2 | Status | `status` | `enum` | ✅ | Status kepegawaian |
| 3 | Status Keaktifan | `status_keaktifan` | `boolean` | ✅ | Active/Inactive |

### 1.2 Informasi Dasar

| # | Field | JSON Key | Type | Required | Keterangan |
|:--|:------|:---------|:-----|:---------|:-----------|
| 4 | Email | `email` | `string` | ✅ | Email institusi |
| 5 | NRP | `nrp` | `string` | ✅ | Nomor Registrasi Pegawai |
| 6 | NIDN | `nidn` | `string` | ❌ | Khusus dosen, nullable |
| 7 | NIP | `nip` | `string` | ❌ | Khusus PNS, nullable |
| 8 | Gelar Depan | `gelar_depan` | `string` | ❌ | Dr., Prof., dll |
| 9 | Nama Lengkap | `nama_lengkap` | `string` | ✅ | — |
| 10 | Gelar Belakang | `gelar_belakang` | `string` | ❌ | M.Kom., Ph.D., dll |
| 11 | Tempat Lahir | `tempat_lahir` | `string` | ✅ | — |
| 12 | Tanggal Lahir | `tanggal_lahir` | `date` | ✅ | Format: `YYYY-MM-DD` |

### 1.3 Kontak

| # | Field | JSON Key | Type | Required | Keterangan |
|:--|:------|:---------|:-----|:---------|:-----------|
| 13 | Telepon | `telp` | `string` | ✅ | Nomor telepon |
| 14 | No WhatsApp | `no_wa` | `string` | ✅ | Nomor WhatsApp |
| 15 | Email Pribadi | `email_pribadi` | `string` | ✅ | — |
| 16 | Jenis Bank | `jenis_bank` | `string` | ✅ | Nama bank |
| 17 | No Rekening | `no_rekening` | `string` | ✅ | — |
| 18 | Atas Nama Rekening | `atas_nama_rekening` | `string` | ✅ | — |

### 1.4 Informasi Kepegawaian Dasar

| # | Field | JSON Key | Type | Required | Keterangan |
|:--|:------|:---------|:-----|:---------|:-----------|
| 19 | Tanggal Masuk | `tanggal_masuk_instansi` | `date` | ✅ | Tanggal pertama masuk |
| 20 | Jenis Pegawai | `jenis_pegawai` | `enum` | ✅ | `PNS` / `Non-PNS` |
| 21 | Kategori Pegawai | `kategori_pegawai` | `enum` | ✅ | `Dosen` / `Tendik` |
| 22 | Status Pegawai | `status_pegawai` | `enum` | ✅ | `Tetap` / `Kontrak` / `Honorer` |

### 1.5 Riwayat Penugasan (Array)

| # | Field | JSON Key | Type | Required | Keterangan |
|:--|:------|:---------|:-----|:---------|:-----------|
| 23 | Unit Kerja | `unit_kerja` | `string/ref` | ✅ | ID/nama unit kerja |
| 24 | Jabatan Struktural | `jabatan_struktural` | `string/ref` | ❌ | Kaprodi, Dekan, dll |
| 25 | Jenis Kepegawaian | `jenis_kepegawaian` | `enum` | ✅ | `Tetap` / `Kontrak` |
| 26 | Tanggal Mulai | `tanggal_mulai` | `date` | ✅ | TMT penugasan |
| 27 | Tanggal Selesai | `tanggal_selesai` | `date` | ❌ | Null jika masih aktif |
| 28 | Penugasan Utama | `penugasan_utama` | `boolean` | ✅ | `true` = primary |

### 1.6 Riwayat Pendidikan (Array)

| # | Field | JSON Key | Type | Required | Keterangan |
|:--|:------|:---------|:-----|:---------|:-----------|
| 29 | Tingkat Pendidikan | `tingkat_pendidikan` | `enum` | ✅ | `SMA`/`D3`/`D4`/`S1`/`S2`/`S3` |
| 30 | Nama Institusi | `nama_institusi` | `string` | ✅ | Sekolah/Universitas |
| 31 | Bidang Studi | `bidang_studi` | `string` | ✅ | — |
| 32 | Tahun Lulus | `tahun_lulus` | `integer` | ✅ | — |

---

## Domain 2: Data Kepegawaian Sekunder

> **Method:** `employee.getSecondary`
> **Deskripsi:** Data lengkap pegawai termasuk dokumen, keluarga, dan publikasi.
> **Total Fields:** 122

### 2.1 Identitas Dasar (15 fields)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 1 | `nrp` | `string` | Primary Key |
| 2 | `nidn` | `string` | Nullable, khusus dosen |
| 3 | `nip` | `string` | Nullable, khusus PNS |
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
| 15 | `aktif` | `boolean` | Status keaktifan |

### 2.2 Alamat KTP (4 fields)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 16 | `alamat_ktp` | `string` | Alamat lengkap KTP |
| 17 | `provinsi_ktp` | `string` | — |
| 18 | `kabupaten_ktp` | `string` | — |
| 19 | `kecamatan_ktp` | `string` | — |

### 2.3 Alamat Domisili (4 fields)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 20 | `alamat_domisili` | `string` | — |
| 21 | `provinsi_domisili` | `string` | — |
| 22 | `kabupaten_domisili` | `string` | — |
| 23 | `kecamatan_domisili` | `string` | — |

### 2.4 Kontak (3 fields)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 24 | `telp` | `string` | — |
| 25 | `no_wa` | `string` | — |
| 26 | `email` | `string` | Email institusi |

### 2.5 Data Fisik & Kesehatan (4 fields)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 27 | `golongan_darah` | `enum` | `A`/`B`/`AB`/`O` |
| 28 | `tinggi_badan` | `integer` | Dalam cm |
| 29 | `berat_badan` | `integer` | Dalam kg |
| 30 | `keterangan_disabilitas` | `string` | Nullable |

### 2.6 Dokumen & Keuangan (6 fields)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 31 | `no_ktp` | `string` | NIK |
| 32 | `no_npwp` | `string` | Nullable |
| 33 | `no_bpjs_tk` | `string` | BPJS Ketenagakerjaan, nullable |
| 34 | `jenis_bank` | `string` | — |
| 35 | `no_rekening` | `string` | — |
| 36 | `atas_nama_rekening` | `string` | — |

### 2.7 Informasi Kepegawaian Dasar (4 fields)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 37 | `tanggal_masuk_instansi` | `date` | — |
| 38 | `jenis_pegawai` | `enum` | `PNS` / `Non-PNS` |
| 39 | `kategori_pegawai` | `enum` | `Dosen` / `Tendik` |
| 40 | `status_pegawai` | `enum` | `Tetap` / `Kontrak` / `Honorer` |

### 2.8 Data CPNS/PNS (4 fields, khusus PNS)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 41 | `tmt_cpns` | `date` | Nullable |
| 42 | `no_sk_cpns` | `string` | Nullable |
| 43 | `tmt_pns` | `date` | Nullable |
| 44 | `no_sk_pns` | `string` | Nullable |

### 2.9 Riwayat Penugasan — Array (10 fields per item)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 45 | `unit_kerja` | `string/ref` | — |
| 46 | `jabatan_struktural` | `string/ref` | Nullable |
| 47 | `jenis_kepegawaian` | `enum` | `Tetap` / `Kontrak` |
| 48 | `tanggal_mulai` | `date` | TMT penugasan |
| 49 | `tanggal_selesai` | `date` | Nullable |
| 50 | `penugasan_utama` | `boolean` | — |
| 51 | `nomor_sk` | `string` | Jika Tetap |
| 52 | `tanggal_sk` | `date` | Jika Tetap |
| 53 | `ditetapkan_oleh` | `string` | Jika Tetap |
| 54 | `dokumen_kontrak` | `string(url)` | Jika Kontrak |

### 2.10 Riwayat Jabatan Fungsional — Array (7 fields per item)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 55 | `jabatan_fungsional` | `enum` | Asisten Ahli / Lektor / Lektor Kepala / Guru Besar |
| 56 | `tmt_jabfung` | `date` | — |
| 57 | `no_sk_jabfung` | `string` | — |
| 58 | `tanggal_sk_jabfung` | `date` | — |
| 59 | `ditetapkan_oleh` | `string` | — |
| 60 | `angka_kredit` | `decimal` | Nullable |
| 61 | `dokumen_sk_jabfung` | `string(url)` | Nullable |

### 2.11 Riwayat Pangkat/Golongan — Array (7 fields per item)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 62 | `pangkat_golongan` | `string` | III/a, III/b, IV/a, dll |
| 63 | `tmt_pangkat` | `date` | — |
| 64 | `no_sk_pangkat` | `string` | — |
| 65 | `tanggal_sk_pangkat` | `date` | — |
| 66 | `ditetapkan_oleh` | `string` | — |
| 67 | `peraturan_dasar` | `string` | Nullable |
| 68 | `dokumen_sk_pangkat` | `string(url)` | Nullable |

### 2.12 Riwayat Pendidikan — Array (8 fields per item)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 69 | `tingkat_pendidikan` | `enum` | `SMA`/`D3`/`D4`/`S1`/`S2`/`S3` |
| 70 | `nama_institusi` | `string` | — |
| 71 | `bidang_studi` | `string` | — |
| 72 | `tahun_lulus` | `integer` | — |
| 73 | `no_ijazah` | `string` | — |
| 74 | `negara` | `string` | — |
| 75 | `kepala_sekolah_dekan` | `string` | Nullable |
| 76 | `dokumen_ijazah` | `string(url)` | Nullable |

### 2.13 Data Pasangan (8 fields)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 77 | `nama_pasangan` | `string` | Nullable jika belum menikah |
| 78 | `tempat_lahir_pasangan` | `string` | — |
| 79 | `tanggal_lahir_pasangan` | `date` | — |
| 80 | `tanggal_nikah` | `date` | — |
| 81 | `no_id_pasangan` | `string` | NIK pasangan |
| 82 | `pekerjaan_pasangan` | `string` | — |
| 83 | `pendidikan_pasangan` | `string` | — |
| 84 | `npwp_pasangan` | `string` | Nullable |

### 2.14 Data Orang Tua — Ayah (3 fields)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 85 | `nama_ayah` | `string` | — |
| 86 | `no_wa_ayah` | `string` | Nullable |
| 87 | `status_ayah` | `enum` | `Hidup` / `Meninggal` |

### 2.15 Data Orang Tua — Ibu (9 fields)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 88 | `nama_ibu` | `string` | — |
| 89 | `tempat_lahir_ibu` | `string` | — |
| 90 | `tanggal_lahir_ibu` | `date` | — |
| 91 | `no_id_ibu` | `string` | NIK |
| 92 | `pekerjaan_ibu` | `string` | — |
| 93 | `pendidikan_ibu` | `string` | — |
| 94 | `npwp_ibu` | `string` | Nullable |
| 95 | `no_wa_ibu` | `string` | Nullable |
| 96 | `status_ibu` | `enum` | `Hidup` / `Meninggal` |

### 2.16 Data Saudara — Array, max 10 (7 fields per item)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 97 | `nama_saudara` | `string` | — |
| 98 | `tempat_lahir_saudara` | `string` | — |
| 99 | `tanggal_lahir_saudara` | `date` | — |
| 100 | `jenis_kelamin_saudara` | `enum` | `L` / `P` |
| 101 | `pekerjaan_saudara` | `string` | — |
| 102 | `pendidikan_saudara` | `string` | — |
| 103 | `no_telp_saudara` | `string` | — |

### 2.17 Data Anak — Array, max 10 (10 fields per item)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
| 104 | `nama_anak` | `string` | — |
| 105 | `tempat_lahir_anak` | `string` | — |
| 106 | `tanggal_lahir_anak` | `date` | — |
| 107 | `urutan_anak` | `integer` | Anak ke-berapa |
| 108 | `hubungan_anak` | `enum` | `Kandung`/`Tiri`/`Angkat` |
| 109 | `jenis_kelamin_anak` | `enum` | `L` / `P` |
| 110 | `pekerjaan_anak` | `string` | Nullable |
| 111 | `pendidikan_anak` | `string` | — |
| 112 | `status_pernikahan_anak` | `enum` | `single`/`married`/… |
| 113 | `no_telp_anak` | `string` | Nullable |

### 2.18 Publikasi — Array (9 fields per item)

| # | JSON Key | Type | Keterangan |
|:--|:---------|:-----|:-----------|
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

## Domain 3: Data Unit Kerja

> **Method:** `workunit.getAll`, `workunit.getById`
> **Deskripsi:** Struktur organisasi unit kerja universitas.
> **Total Fields:** 7

| # | JSON Key | Type | Required | Keterangan |
|:--|:---------|:-----|:---------|:-----------|
| 1 | `id` | `string` | ✅ | Primary Key (UUID/ObjectId) |
| 2 | `name` | `string` | ✅ | Nama unit kerja |
| 3 | `code` | `string` | ✅ | Kode singkat (FST, TI, DPMP, dll) |
| 4 | `is_active` | `boolean` | ✅ | Status aktif |
| 5 | `parent_id` | `string` | ❌ | Nullable jika top level |
| 6 | `is_top_most` | `boolean` | ✅ | `true` jika level universitas |
| 7 | `type` | `enum` | ✅ | `Pendidikan` / `Non-Pendidikan` |

---

## Domain 4: Data Jabatan

> **Method:** `position.getAll`, `position.getByWorkunit`
> **Deskripsi:** Struktur jabatan per unit kerja.
> **Total Fields:** 8

| # | JSON Key | Type | Required | Keterangan |
|:--|:---------|:-----|:---------|:-----------|
| 1 | `id` | `string` | ✅ | Primary Key (UUID/ObjectId) |
| 2 | `name` | `string` | ✅ | Nama jabatan |
| 3 | `code` | `string` | ✅ | Kode jabatan |
| 4 | `is_active` | `boolean` | ✅ | Status aktif |
| 5 | `parent_id` | `string` | ❌ | Nullable jika top level |
| 6 | `parent_name` | `string` | ❌ | Denormalized untuk display |
| 7 | `workunit_id` | `string` | ✅ | ID unit kerja |
| 8 | `is_top_most` | `boolean` | ✅ | `true` jika jabatan tertinggi |

---

## Enumerasi Standar

### Jenis Pegawai
```
PNS | Non-PNS
```

### Kategori Pegawai
```
Dosen | Tendik
```

### Status Pegawai
```
Tetap | Kontrak | Honorer
```

### Jenis Kelamin
```
L | P
```

### Status Pernikahan
```
single | married | divorced | widowed
```

### Tingkat Pendidikan
```
SMA | D3 | D4 | S1 | S2 | S3
```

### Jabatan Fungsional
```
Asisten Ahli | Lektor | Lektor Kepala | Guru Besar
```

### Golongan Darah
```
A | B | AB | O
```

### Tipe Unit Kerja
```
Pendidikan | Non-Pendidikan
```
