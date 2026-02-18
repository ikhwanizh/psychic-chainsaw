# Service Catalog — UNU Master Data API

## Summary

This document maps **all service consumers** that will access the Master Data API along with the **specific data fields** required by each service. The goal is to eliminate data redundancy by only returning the fields needed.

---

## Service → Data Field Mapping

### 1. SIOBA (Academic Information System)

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Web |
| **Process** | Module management, role management, user management, curriculum, class management, RPKPS, exams, portfolio |

**Required fields:**

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

### 2. Presensi (Attendance System)

#### 2.1 Mobile Apps — Clock In/Out

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Mobile Apps |
| **Process** | Clock in/out, leave requests, attendance history |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `tipe_jam_kerja`

#### 2.2 Internal System — Approval & Monitoring

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Internal System |
| **Process** | Attendance approval and monitoring |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `izin_akses_backoffice_presensi`

---

### 3. Agenda & Calendar

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Mobile Apps & Greenteam Web |
| **Process** | Create agendas (room booking, invitations), calendar, attendance verification |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `no_wa`

---

### 4. Ruang (Room Booking)

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Internal System |
| **Process** | Room booking approval |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `izin_akses_backoffice_ruang`

---

### 5. Kupon (Voucher Service)

#### 5.1 Greenteam Web & Internal System

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Greenteam Web & Internal System |
| **Process** | Scan vouchers, history, usage monitoring |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `izin_akses_backoffice_kupon`

---

### 6. Cuti (Leave Request)

#### 6.1 Greenteam Web & Mobile — Submission

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Greenteam Web & Mobile Apps |
| **Process** | Leave requests and history |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `id_atasan_langsung`, `sisa_cuti`

#### 6.2 Internal System — Approval

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Internal System |
| **Process** | Approval by superiors and admins |

**Fields:** `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `akses_approval_cuti`, `daftar_bawahan_langsung`

---

### 7. Peminjaman Barang (Asset Loan)

#### 7.1 Greenteam Web — Submission

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Greenteam Web |
| **Process** | Loan requests and history |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `no_wa`

#### 7.2 Internal System — Approval

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Internal System |
| **Process** | Loan approval |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `akses_approval_peminjaman`, `daftar_bawahan_langsung`

---

### 8. Dokumen (Document Management)

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Internal System, Greenteam Web, Mobile Apps |
| **Process** | Upload, check, sign, archive, send, and view documents |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `izin_akses_backoffice_dokumen`

---

### 9. Anggaran (Budget/Finance)

| Aspect | Detail |
|:-------|:-------|
| **Platform** | Mobile Apps & Back Office |
| **Process** | Budget requests, accountability reports, approvals, disbursements |

**Fields:** `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `unit_kerja`, `jabatan_struktural`, `no_wa`, `no_rekening`, `jenis_bank`, `izin_akses_backoffice_anggaran`

---

### 10. Special Services

#### 10.1 Ijazah (Diploma)
| **Process** | Diploma printing |
|:-----------|:----------------|
| **Fields** | `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `akses_ijazah` |

#### 10.2 CMS Web UNU
| **Process** | Content management |
|:-----------|:------------------|
| **Fields** | `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `akses_cms` |

#### 10.3 Ulang Tahun (Birthday)
| **Process** | Birthday greetings |
|:-----------|:-------------------|
| **Fields** | `id_pegawai`, `status`, `status_keaktifan`, `nrp`, `email`, `nama_lengkap`, `tanggal_lahir` |

---

## Field Requirements Summary Per Service

| # | Service | Total Fields | Domain |
|:--|:--------|:-------------|:-------|
| 1 | SIOBA | 9 | Primary |
| 2 | Presensi (Mobile) | 9 | Primary |
| 3 | Presensi (Internal) | 9 | Primary |
| 4 | Agenda & Calendar | 9 | Primary |
| 5 | Ruang | 9 | Primary |
| 6 | Kupon | 9 | Primary |
| 7 | Cuti (Submission) | 10 | Primary |
| 8 | Cuti (Approval) | 5 | Primary |
| 9 | Peminjaman (Submission) | 7 | Primary |
| 10 | Peminjaman (Approval) | 8 | Primary |
| 11 | Dokumen | 9 | Primary |
| 12 | Anggaran | 12 | Primary |
| 13 | Ijazah | 7 | Primary |
| 14 | CMS Web | 7 | Primary |
| 15 | Ulang Tahun | 7 | Primary |

> **Insight:** Most services only need **7–12 fields** from the Primary domain, while the Secondary data contains **122 fields**. Without field selection, each request returns ~110 unnecessary fields.

---

## JSON-RPC Method Registry

| Method | Description | Domain |
|:-------|:------------|:-------|
| `employee.getPrimary` | Primary employee data per employee | Primary |
| `employee.getSecondary` | Secondary employee data per employee | Secondary |
| `employee.search` | Search employees by criteria | Primary |
| `employee.getByWorkunit` | List employees per work unit | Primary |
| `employee.getByPosition` | List employees per position | Primary |
| `workunit.getAll` | All work units | Work Unit |
| `workunit.getById` | Specific work unit details | Work Unit |
| `workunit.getTree` | Work unit hierarchy | Work Unit |
| `position.getAll` | All positions | Position |
| `position.getByWorkunit` | Positions per work unit | Position |
| `position.getById` | Specific position details | Position |
| `service.getFieldMapping` | Field mapping per service | Meta |
