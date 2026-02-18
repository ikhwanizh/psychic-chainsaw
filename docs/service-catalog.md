# Service Catalog — UNU Master Data API

## Ringkasan

Dokumen ini memetakan **seluruh service consumer** yang akan mengakses Master Data API beserta **data field spesifik** yang dibutuhkan oleh masing-masing service. Tujuannya adalah menghilangkan redundansi data dengan hanya mengembalikan field yang diperlukan.

---

## Pemetaan Service → Data Fields

### 1. SIOBA (Sistem Informasi Akademik)

| Aspek | Detail |
|:------|:-------|
| **Platform** | Web |
| **Proses** | Module management, role management, user management, curriculum, class management, RPKPS, ujian, portfolio |

**Fields yang dibutuhkan:**

```json
{
  "method": "employee.getPrimary",
  "params": {
    "fields": [
      "id_pegawai",
      "status",
      "status_keaktifan",
      "nrp",
      "email",
      "nama_lengkap",
      "unit_kerja",
      "jabatan_struktural"
    ],
    "extra": ["role_oba"]
  }
}
```

---

### 2. Presensi (Sistem Kehadiran)

#### 2.1 Mobile Apps — Clock In/Out

| Aspek | Detail |
|:------|:-------|
| **Platform** | Mobile Apps |
| **Proses** | Absensi masuk/keluar, pengajuan izin, riwayat kehadiran |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `tipe_jam_kerja`

#### 2.2 Sistem Internal — Approval & Monitoring

| Aspek | Detail |
|:------|:-------|
| **Platform** | Sistem Internal |
| **Proses** | Persetujuan dan monitoring kehadiran |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `izin_akses_backoffice_presensi`

---

### 3. Agenda & Calendar

| Aspek | Detail |
|:------|:-------|
| **Platform** | Mobile Apps & Greenteam Web |
| **Proses** | Pembuatan agenda (booking ruang, undangan), kalender, verifikasi kehadiran |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `no_wa`

---

### 4. Ruang (Room Booking)

| Aspek | Detail |
|:------|:-------|
| **Platform** | Sistem Internal |
| **Proses** | Persetujuan peminjaman ruang |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `izin_akses_backoffice_ruang`

---

### 5. Kupon (Voucher Service)

#### 5.1 Greenteam Web & Sistem Internal

| Aspek | Detail |
|:------|:-------|
| **Platform** | Greenteam Web & Sistem Internal |
| **Proses** | Scan kupon, riwayat, monitoring penggunaan |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `izin_akses_backoffice_kupon`

---

### 6. Cuti (Leave Request)

#### 6.1 Greenteam Web & Mobile — Pengajuan

| Aspek | Detail |
|:------|:-------|
| **Platform** | Greenteam Web & Mobile Apps |
| **Proses** | Pengajuan cuti dan riwayat |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `id_atasan_langsung`, `sisa_cuti`

#### 6.2 Sistem Internal — Approval

| Aspek | Detail |
|:------|:-------|
| **Platform** | Sistem Internal |
| **Proses** | Persetujuan oleh pimpinan dan admin |

**Fields:** `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `akses_approval_cuti`, `daftar_bawahan_langsung`

---

### 7. Peminjaman Barang (Asset Loan)

#### 7.1 Greenteam Web — Pengajuan

| Aspek | Detail |
|:------|:-------|
| **Platform** | Greenteam Web |
| **Proses** | Pengajuan dan riwayat peminjaman |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `no_wa`

#### 7.2 Sistem Internal — Approval

| Aspek | Detail |
|:------|:-------|
| **Platform** | Sistem Internal |
| **Proses** | Persetujuan peminjaman |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `akses_approval_peminjaman`, `daftar_bawahan_langsung`

---

### 8. Dokumen (Document Management)

| Aspek | Detail |
|:------|:-------|
| **Platform** | Sistem Internal, Greenteam Web, Mobile Apps |
| **Proses** | Upload, cek, tanda tangan, arsip, kirim, dan lihat dokumen |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `izin_akses_backoffice_dokumen`

---

### 9. Anggaran (Budget/Finance)

| Aspek | Detail |
|:------|:-------|
| **Platform** | Mobile Apps & Back Office |
| **Proses** | Pengajuan, LPJ, persetujuan, pencairan |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `no_wa`, `no_rekening`, `jenis_bank`, `izin_akses_backoffice_anggaran`

---

### 10. Service Khusus

#### 10.1 Ijazah
| **Proses** | Cetak ijazah |
|:-----------|:-------------|
| **Fields** | `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `akses_ijazah` |

#### 10.2 CMS Web UNU
| **Proses** | Content management |
|:-----------|:------------------|
| **Fields** | `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `akses_cms` |

#### 10.3 Ulang Tahun (Birthday)
| **Proses** | Ucapan ulang tahun |
|:-----------|:-------------------|
| **Fields** | `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `tanggal_lahir` |

---

## Ringkasan Kebutuhan Field Per Service

| # | Service | Total Fields | Domain |
|:--|:--------|:-------------|:-------|
| 1 | SIOBA | 9 | Primer |
| 2 | Presensi (Mobile) | 9 | Primer |
| 3 | Presensi (Internal) | 9 | Primer |
| 4 | Agenda & Calendar | 9 | Primer |
| 5 | Ruang | 9 | Primer |
| 6 | Kupon | 9 | Primer |
| 7 | Cuti (Pengajuan) | 10 | Primer |
| 8 | Cuti (Approval) | 5 | Primer |
| 9 | Peminjaman (Pengajuan) | 7 | Primer |
| 10 | Peminjaman (Approval) | 8 | Primer |
| 11 | Dokumen | 9 | Primer |
| 12 | Anggaran | 12 | Primer |
| 13 | Ijazah | 7 | Primer |
| 14 | CMS Web | 7 | Primer |
| 15 | Ulang Tahun | 7 | Primer |

> **Insight:** Sebagian besar service hanya membutuhkan **7–12 field** dari domain Primer, sedangkan data Sekunder berisi **122 field**. Tanpa field selection, setiap request mengembalikan ~110 field yang tidak diperlukan.

---

## JSON-RPC Method Registry

| Method | Deskripsi | Domain |
|:-------|:----------|:-------|
| `employee.getPrimary` | Data kepegawaian primer per pegawai | Primer |
| `employee.getSecondary` | Data kepegawaian sekunder per pegawai | Sekunder |
| `employee.search` | Cari pegawai berdasarkan kriteria | Primer |
| `employee.getByWorkunit` | Daftar pegawai per unit kerja | Primer |
| `employee.getByPosition` | Daftar pegawai per jabatan | Primer |
| `workunit.getAll` | Semua unit kerja | Unit Kerja |
| `workunit.getById` | Detail unit kerja spesifik | Unit Kerja |
| `workunit.getTree` | Hierarki unit kerja | Unit Kerja |
| `position.getAll` | Semua jabatan | Jabatan |
| `position.getByWorkunit` | Jabatan per unit kerja | Jabatan |
| `position.getById` | Detail jabatan spesifik | Jabatan |
| `service.getFieldMapping` | Mapping field per service | Meta |
