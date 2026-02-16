# SISTEM E-ADUAN - MERMAID DIAGRAMS
## Jabatan Imigresen Malaysia
**Version:** 2.0 (with Pegawai Operasi)  
**Date:** 16 February 2026

---

## 1. MAIN WORKFLOW - Complete Flow

```mermaid
flowchart TD
    Start([Pengadu<br/>Register/Login]) --> Submit[Buat Aduan Baru<br/>Upload Bukti]
    Submit --> Baru[📥 BARU<br/>Status: New]
    
    Baru --> |Notifikasi Auto| Pegawai[Pegawai Penerima HQ]
    Pegawai --> Review{Semak<br/>Kelengkapan?}
    
    Review --> |Lengkap| Lengkap[✅ LENGKAP<br/>Complete]
    Review --> |Tidak Lengkap| TidakLengkap[❌ TIDAK_LENGKAP<br/>Return to Pengadu]
    
    TidakLengkap --> Kemaskini{Pengadu<br/>Kemaskini?}
    Kemaskini --> |Ya, dalam 30 hari| Submit
    Kemaskini --> |Tidak/Timeout| Ditolak[🚫 DITOLAK<br/>Rejected]
    
    Lengkap --> KetuaHQ[Ketua Penerimaan HQ<br/>Review & Approve]
    KetuaHQ --> |Lulus| Agihan[⏳ MENUNGGU_AGIHAN<br/>Pending Assignment]
    KetuaHQ --> |Reject| TidakLengkap
    
    Agihan --> PengarahHQ[Pengarah Penguatkuasaan HQ<br/>Pilih Negeri]
    PengarahHQ --> Tindakan[⚙️ DALAM_TINDAKAN<br/>Assigned to State]
    
    Tindakan --> KetuaNegeri[Ketua Penerimaan Negeri<br/>Review]
    KetuaNegeri --> PengarahNegeri[Pengarah Negeri<br/>Assign Pegawai Operasi]
    
    PengarahNegeri --> |Assign| PegawaiOp[🚔 DI_PEGAWAI_OPERASI<br/>Assigned to Officer]
    PengarahNegeri --> |Reject| Agihan
    
    PegawaiOp --> MulaOp[Pegawai Operasi<br/>Klik 'Mula Operasi']
    MulaOp --> DalamOp[🚨 DALAM_OPERASI<br/>Operation In Progress<br/>GPS Tracking ON]
    
    DalamOp --> Execute[Di Lokasi:<br/>- Jalankan operasi<br/>- Tangkap PATI<br/>- Ambil bukti<br/>- Isi laporan]
    
    Execute --> Submit2[Submit Laporan<br/>ke Pengarah Negeri]
    
    Submit2 --> Review2{Pengarah<br/>Review}
    Review2 --> |Approve| Selesai[✅ SELESAI<br/>Completed]
    Review2 --> |Need Update| DalamOp
    Review2 --> |Major Issue| Ditolak
    
    Selesai --> Feedback{Pengadu<br/>Beri Rating?}
    Feedback --> |Puas Hati| End([END<br/>Archive after 90 days])
    Feedback --> |Tak Puas Hati| Rayuan[Buat Rayuan<br/>Investigation]
    Rayuan --> End
    
    Ditolak --> End
    
    style Baru fill:#e3f2fd
    style Lengkap fill:#c8e6c9
    style TidakLengkap fill:#ffcdd2
    style Agihan fill:#fff9c4
    style Tindakan fill:#e1bee7
    style PegawaiOp fill:#ffecb3
    style DalamOp fill:#bbdefb
    style Selesai fill:#a5d6a7
    style Ditolak fill:#ef9a9a
```

---

## 2. ROLE HIERARCHY - Organization Structure

```mermaid
graph TD
    subgraph System["System Administrators"]
        SuperAdmin[👑 Super Admin<br/>God Mode<br/>Full Access]
        Admin[🔧 Admin<br/>System Management<br/>User Admin]
    end
    
    subgraph HQ["HEADQUARTERS - Level 1"]
        PengarahHQ[🎖️ Pengarah Penguatkuasaan HQ<br/>- Agih ke negeri<br/>- Oversee all states<br/>- Generate KPI]
        KetuaHQ[👔 Ketua Penerimaan HQ<br/>- Luluskan aduan<br/>- Pantau pegawai<br/>- QA review]
        PegawaiHQ[📥 Pegawai Penerima HQ<br/>- Terima aduan baru<br/>- Semak kelengkapan<br/>- Verify documents]
    end
    
    subgraph State["STATE LEVEL - Level 2<br/>(16 Negeri)"]
        PengarahNegeri[🎖️ Pengarah Negeri<br/>- Assign pegawai operasi<br/>- Approve operations<br/>- State KPI]
        KetuaNegeri[📥 Ketua Penerimaan Negeri<br/>- Terima dari HQ<br/>- Cadang pegawai<br/>- Coordinate state]
        PegawaiOperasi[🚔 Pegawai Operasi 🆕<br/>- Field operations<br/>- Tangkap PATI<br/>- Submit reports]
    end
    
    subgraph Public["PUBLIC USERS"]
        Pengadu[🧑 Pengadu<br/>- Buat aduan<br/>- Upload bukti<br/>- Check status<br/>- Beri feedback]
    end
    
    SuperAdmin -.-> Admin
    Admin -.-> PengarahHQ
    
    PengarahHQ --> KetuaHQ
    KetuaHQ --> PegawaiHQ
    
    PengarahHQ -.-> |Assign to State| PengarahNegeri
    PengarahNegeri --> KetuaNegeri
    KetuaNegeri --> PegawaiOperasi
    
    Pengadu -.-> |Submit Aduan| PegawaiHQ
    PegawaiOperasi -.-> |Complete| Pengadu
    
    style SuperAdmin fill:#f8bbd0
    style Admin fill:#f8bbd0
    style PengarahHQ fill:#c5cae9
    style KetuaHQ fill:#c5cae9
    style PegawaiHQ fill:#c5cae9
    style PengarahNegeri fill:#b2dfdb
    style KetuaNegeri fill:#b2dfdb
    style PegawaiOperasi fill:#ffe0b2
    style Pengadu fill:#f0f4c3
```

---

## 3. STATE TRANSITION DIAGRAM

```mermaid
stateDiagram-v2
    [*] --> BARU: Pengadu submit
    
    BARU --> SEMAKAN_KELENGKAPAN: Pegawai Penerima<br/>start review
    
    SEMAKAN_KELENGKAPAN --> LENGKAP: Approved
    SEMAKAN_KELENGKAPAN --> TIDAK_LENGKAP: Missing info
    SEMAKAN_KELENGKAPAN --> DITOLAK: Invalid/Spam
    
    TIDAK_LENGKAP --> BARU: Pengadu resubmit<br/>(within 30 days)
    TIDAK_LENGKAP --> DITOLAK: Timeout or<br/>3x rejection
    
    LENGKAP --> MENUNGGU_AGIHAN: Ketua HQ approve
    LENGKAP --> TIDAK_LENGKAP: Ketua HQ reject
    
    MENUNGGU_AGIHAN --> DALAM_TINDAKAN: Pengarah HQ<br/>assign to state
    MENUNGGU_AGIHAN --> DITOLAK: Invalid
    
    DALAM_TINDAKAN --> DI_PEGAWAI_OPERASI: Pengarah Negeri<br/>assign officer
    DALAM_TINDAKAN --> MENUNGGU_AGIHAN: Return to HQ
    DALAM_TINDAKAN --> DITOLAK: Cannot proceed
    
    DI_PEGAWAI_OPERASI --> DALAM_OPERASI: Pegawai start operation
    DI_PEGAWAI_OPERASI --> DALAM_TINDAKAN: Reassign
    
    DALAM_OPERASI --> SELESAI: Operation completed<br/>& approved
    DALAM_OPERASI --> DI_PEGAWAI_OPERASI: Need report update
    DALAM_OPERASI --> DITOLAK: Operation failed
    
    SELESAI --> [*]: Archive after 90 days
    DITOLAK --> [*]: Archive after 90 days
    
    note right of DI_PEGAWAI_OPERASI
        🆕 NEW STAGE
        Pegawai Operasi role
        Version 2.0
    end note
```

---

## 4. SEQUENCE DIAGRAM - User Journey

```mermaid
sequenceDiagram
    actor Pengadu
    participant System as E-Aduan System
    participant PegawaiHQ as Pegawai Penerima HQ
    participant KetuaHQ as Ketua Penerimaan HQ
    participant PengarahHQ as Pengarah HQ
    participant PengarahNegeri as Pengarah Negeri
    participant PegawaiOp as Pegawai Operasi 🆕
    participant DB as Database
    
    Pengadu->>System: 1. Register/Login
    Pengadu->>System: 2. Buat Aduan + Upload Bukti
    System->>DB: Save aduan (Status: BARU)
    System->>Pengadu: Email/SMS Confirmation
    System->>PegawaiHQ: 🔔 Notifikasi Aduan Baru
    
    PegawaiHQ->>System: 3. Review Kelengkapan
    alt Lengkap
        PegawaiHQ->>DB: Update Status: LENGKAP
        DB->>KetuaHQ: 🔔 Notifikasi untuk approve
        KetuaHQ->>System: 4. Review & Approve
        KetuaHQ->>DB: Update Status: MENUNGGU_AGIHAN
        DB->>PengarahHQ: 🔔 Notifikasi untuk assign
    else Tidak Lengkap
        PegawaiHQ->>DB: Update Status: TIDAK_LENGKAP
        DB->>Pengadu: 📱 SMS + Email (Kemaskini)
        Pengadu->>System: Kemaskini & Resubmit
    end
    
    PengarahHQ->>System: 5. Pilih Negeri & Assign
    PengarahHQ->>DB: Update Status: DALAM_TINDAKAN
    DB->>PengarahNegeri: 🔔 Notifikasi Aduan Baru
    
    PengarahNegeri->>System: 6. Review & Assign Pegawai Operasi
    PengarahNegeri->>DB: Update Status: DI_PEGAWAI_OPERASI
    DB->>PegawaiOp: 📱 SMS + Email + Push (Tugasan Baru)
    
    PegawaiOp->>System: 7. View Aduan di Mobile App
    PegawaiOp->>System: 8. Klik "Mula Operasi"
    System->>DB: Update Status: DALAM_OPERASI
    DB->>Pengadu: 📱 SMS (Pegawai on the way)
    
    Note over PegawaiOp: 9. Pergi ke lokasi<br/>GPS tracking active
    
    PegawaiOp->>System: 10. Sampai lokasi (GPS confirm)
    System->>Pengadu: 📱 SMS (Pegawai arrived)
    
    Note over PegawaiOp: 11. Jalankan operasi<br/>Tangkap PATI<br/>Ambil gambar/video
    
    PegawaiOp->>System: 12. Submit Laporan Operasi
    System->>DB: Save laporan + bukti
    DB->>PengarahNegeri: 🔔 Notifikasi Laporan Ready
    
    PengarahNegeri->>System: 13. Review Laporan
    alt Approve
        PengarahNegeri->>DB: Update Status: SELESAI
        DB->>Pengadu: 📱 SMS + Email (Case closed)
        Pengadu->>System: 14. Beri Rating ⭐⭐⭐⭐⭐
    else Need Update
        PengarahNegeri->>DB: Request correction
        DB->>PegawaiOp: 🔔 Update laporan
    end
```

---

## 5. TIMELINE GANTT CHART

```mermaid
gantt
    title E-Aduan Workflow Timeline (Target: 7 Days)
    dateFormat  YYYY-MM-DD
    section Pengadu
    Submit Aduan           :done, submit, 2026-02-10, 1h
    
    section HQ Processing
    Semakan Kelengkapan    :active, semak, 2026-02-10, 24h
    Kelulusan Ketua HQ     :ketua, after semak, 12h
    Agihan Pengarah HQ     :agih, after ketua, 24h
    
    section State Processing
    Review Ketua Negeri    :negeri1, after agih, 24h
    Assign Pengarah Negeri :negeri2, after negeri1, 24h
    
    section Field Operations
    Pegawai Terima Tugasan :crit, pegawai1, after negeri2, 12h
    Rancang Operasi        :pegawai2, after pegawai1, 48h
    Jalankan Operasi       :crit, operasi, after pegawai2, 8h
    Submit Laporan         :laporan, after operasi, 6h
    
    section Closure
    Pengarah Review        :review, after laporan, 12h
    Selesai & Archive      :done, selesai, after review, 1h
    
    section SLA Markers
    Day 1 Target           :milestone, day1, 2026-02-11, 0h
    Day 3 Target           :milestone, day3, 2026-02-13, 0h
    Day 7 Target (MAX)     :milestone, day7, 2026-02-17, 0h
```

---

## 6. GEOGRAPHY - 16 States Coverage

```mermaid
mindmap
    root((E-Aduan System<br/>16 Negeri))
        (Semenanjung Malaysia)
            [Utara]
                Perlis
                Kedah
                Pulau Pinang
                Perak
            [Tengah]
                Selangor
                WP Kuala Lumpur
                WP Putrajaya
                Negeri Sembilan
            [Timur]
                Pahang
                Terengganu
                Kelantan
            [Selatan]
                Johor
                Melaka
        (Malaysia Timur)
            [Sabah]
                Pantai Timur
                Pantai Barat
                Pedalaman
                WP Labuan
            [Sarawak]
                Kuching
                Miri
                Sibu
                Bintulu
```

---

## 7. PERMISSION MATRIX - Access Control

```mermaid
graph LR
    subgraph Roles
        A1[Pengadu]
        A2[Pegawai HQ]
        A3[Ketua HQ]
        A4[Pengarah HQ]
        A5[Negeri Staff]
        A6[Pegawai Operasi 🆕]
        A7[Admin]
    end
    
    subgraph Actions
        B1[Buat Aduan]
        B2[Semak Kelengkapan]
        B3[Lulus Aduan]
        B4[Agih ke Negeri]
        B5[Assign Pegawai]
        B6[Jalankan Operasi]
        B7[Tutup Aduan]
        B8[Manage System]
    end
    
    A1 -->|✅| B1
    A2 -->|✅| B2
    A3 -->|✅| B3
    A4 -->|✅| B4
    A5 -->|✅| B5
    A6 -->|✅| B6
    A5 -->|✅| B7
    A7 -->|✅| B8
    
    style A1 fill:#f0f4c3
    style A2 fill:#c5cae9
    style A3 fill:#c5cae9
    style A4 fill:#c5cae9
    style A5 fill:#b2dfdb
    style A6 fill:#ffe0b2
    style A7 fill:#f8bbd0
```

---

## 8. DATA FLOW DIAGRAM

```mermaid
graph TD
    A[Pengadu Input] -->|Submit Form| B[Web Server<br/>PHP]
    B -->|Validate| C{Input Valid?}
    C -->|Yes| D[Database<br/>MySQL]
    C -->|No| E[Error Message]
    E --> A
    
    D -->|Save| F[Table: aduan]
    D -->|Save| G[Table: lampiran]
    D -->|Log| H[Table: audit_logs]
    
    F -->|Trigger| I[Notification Service]
    I -->|Email| J[SMTP Server]
    I -->|SMS| K[SMS Gateway]
    I -->|Push| L[Firebase Cloud Messaging]
    
    J -->|Send| M[Recipient Email]
    K -->|Send| N[Recipient Phone]
    L -->|Send| O[Mobile App]
    
    D -->|Read by| P[Pegawai HQ Dashboard]
    D -->|Read by| Q[State Dashboard]
    D -->|Read by| R[Pegawai Operasi Mobile App 🆕]
    
    R -->|GPS Data| S[Google Maps API]
    R -->|Upload Evidence| T[File Storage<br/>uploads/]
    T -->|Backup| U[Cloud Storage<br/>AWS S3]
    
    P -->|Update Status| D
    Q -->|Update Status| D
    R -->|Submit Report| D
    
    style D fill:#c8e6c9
    style I fill:#ffe0b2
    style R fill:#bbdefb
    style T fill:#f8bbd0
```

---

## 9. KPI DASHBOARD - Metrics Overview

```mermaid
pie title Distribution of Aduan by Status (Current Month)
    "✅ Selesai (52%)" : 1847
    "🚨 Dalam Operasi (12%)" : 420
    "⚙️ Dalam Tindakan (18%)" : 640
    "⏳ Menunggu Agihan (8%)" : 285
    "🔍 Semakan (7%)" : 248
    "📥 Baru (3%)" : 110
```

```mermaid
pie title Top 5 Negeri by Complaint Volume
    "Johor (24%)" : 342
    "Selangor (20%)" : 289
    "Sabah (16%)" : 234
    "Kuala Lumpur (13%)" : 187
    "Sarawak (11%)" : 156
    "Others (16%)" : 230
```

```mermaid
xychart-beta
    title "Average Resolution Time (Days) - Last 6 Months"
    x-axis [Sep, Oct, Nov, Dec, Jan, Feb]
    y-axis "Days" 0 --> 12
    line [9.2, 8.9, 8.5, 8.2, 8.0, 8.2]
    line [7.0, 7.0, 7.0, 7.0, 7.0, 7.0]
```

---

## 10. OPERATION SCENARIOS - Field Work

```mermaid
flowchart TD
    Start([Pegawai Operasi<br/>Terima Tugasan]) --> Plan[Rancang Operasi:<br/>- Tentukan tarikh<br/>- Siapkan peralatan<br/>- Koordinasi]
    
    Plan --> Travel[Pergi ke Lokasi<br/>GPS Tracking ON]
    Travel --> Arrive[Sampai di Lokasi<br/>GPS Confirm]
    Arrive --> Inspect{Keadaan<br/>di Lokasi?}
    
    Inspect --> |PATI Dijumpai| Action1[Operasi Berjaya ✅]
    Inspect --> |Tiada PATI| Action2[Operasi Tidak Berjaya 🔍]
    Inspect --> |Tidak Selamat| Action3[Operasi Dibatalkan ⚠️]
    
    Action1 --> Evidence1[Ambil Bukti:<br/>- Tangkap PATI<br/>- Foto/video<br/>- Rekod bilangan<br/>- Interview]
    Action2 --> Evidence2[Ambil Bukti:<br/>- Foto lokasi kosong<br/>- Interview jiran<br/>- Dokumen alasan]
    Action3 --> Evidence3[Ambil Bukti:<br/>- Foto situasi<br/>- Dokumen sebab<br/>- Safety concerns]
    
    Evidence1 --> Report1[Laporan:<br/>Bilangan PATI: 15<br/>Warganegara: Bangladesh<br/>Tindakan: Tangkapan<br/>Compound: RM 50,000]
    Evidence2 --> Report2[Laporan:<br/>Tiada PATI dijumpai<br/>Alasan: Premis kosong<br/>Recommendation: Close]
    Evidence3 --> Report3[Laporan:<br/>Operasi dibatalkan<br/>Alasan: Cuaca buruk<br/>Recommendation: Reschedule]
    
    Report1 --> Submit[Upload Bukti +<br/>Submit Laporan]
    Report2 --> Submit
    Report3 --> Submit
    
    Submit --> Review{Pengarah<br/>Negeri<br/>Review}
    
    Review --> |Approve| Success[✅ SELESAI<br/>Case Closed]
    Review --> |Need Info| Update[Request More Info]
    Update --> Evidence1
    
    Success --> End([Notifikasi Pengadu<br/>Request Feedback])
    
    style Action1 fill:#c8e6c9
    style Action2 fill:#fff9c4
    style Action3 fill:#ffcdd2
    style Success fill:#a5d6a7
```

---

## 11. NOTIFICATION FLOW

```mermaid
flowchart LR
    subgraph Events
        E1[Aduan Baru]
        E2[Status Update]
        E3[Tugasan Baru]
        E4[Operasi Start]
        E5[Operasi Selesai]
        E6[Overdue Alert]
    end
    
    subgraph NotificationService[Notification Service]
        NS[Queue Manager]
    end
    
    subgraph Channels
        Email[📧 Email<br/>SMTP]
        SMS[📱 SMS<br/>Gateway]
        Push[🔔 Push<br/>FCM]
    end
    
    subgraph Recipients
        R1[Pengadu]
        R2[Pegawai HQ]
        R3[Pegawai Operasi]
        R4[Pengarah]
    end
    
    E1 --> NS
    E2 --> NS
    E3 --> NS
    E4 --> NS
    E5 --> NS
    E6 --> NS
    
    NS --> Email
    NS --> SMS
    NS --> Push
    
    Email --> R1
    Email --> R2
    Email --> R3
    Email --> R4
    
    SMS --> R1
    SMS --> R3
    SMS --> R4
    
    Push --> R2
    Push --> R3
    Push --> R4
    
    style NS fill:#ffe0b2
    style Email fill:#bbdefb
    style SMS fill:#c8e6c9
    style Push fill:#f8bbd0
```

---

## 12. DATABASE ER DIAGRAM (Simplified)

```mermaid
erDiagram
    PENGADU ||--o{ ADUAN : submits
    PENTADBIR ||--o{ ADUAN : handles
    NEGERI ||--o{ ADUAN : location
    KATEGORI_ADUAN ||--o{ ADUAN : categorizes
    ADUAN ||--o{ LAMPIRAN : has
    ADUAN ||--o{ VIDEO_LAMPIRAN : has
    ADUAN ||--o{ ADUAN_KOMEN : has
    ADUAN ||--o{ ADUAN_WORKFLOW_HISTORY : tracks
    ADUAN ||--o{ ADUAN_HISTORY : logs
    PENGADU ||--o{ NOTIFIKASI_PENGADU : receives
    PENTADBIR ||--o{ NOTIFIKASI_PENTADBIR : receives
    
    PENGADU {
        int id PK
        string nama_penuh
        string email UK
        string password
        string no_kp
        int negeri_id FK
        enum status
    }
    
    PENTADBIR {
        int id PK
        string nama_penuh
        string email UK
        string password
        enum workflow_role
        int negeri_id FK
        enum status
    }
    
    ADUAN {
        int id PK
        int id_pengadu FK
        int kategori_aduan_id FK
        int negeri_id FK
        int pegawai_penerima_id FK
        int pengarah_id FK
        int pegawai_operasi_id FK "NEW v2.0"
        string pegawai_operasi_nama "NEW v2.0"
        enum workflow_stage
        text butiran_aduan
        float latitude
        float longitude
        datetime tarikh_aduan
        datetime tarikh_selesai
    }
    
    NEGERI {
        int id PK
        string nama
        string kod
        int urutan
    }
    
    KATEGORI_ADUAN {
        int id PK
        string nama UK
        text penerangan
        enum status
    }
    
    LAMPIRAN {
        int id PK
        int aduan_id FK
        string jenis_fail
        string nama_fail
        string fail_path
        datetime tarikh_upload
    }
    
    VIDEO_LAMPIRAN {
        int id PK
        int aduan_id FK
        string nama_fail
        string fail_path
        int durasi
        datetime tarikh_upload
    }
    
    ADUAN_KOMEN {
        int id PK
        int aduan_id FK
        int user_id FK
        enum user_type
        text komen
        datetime tarikh_komen
    }
    
    ADUAN_WORKFLOW_HISTORY {
        int id PK
        int aduan_id FK
        string from_status
        string to_status
        int changed_by FK
        text change_reason
        datetime created_at
    }
```

---

## HOW TO USE THESE DIAGRAMS

### In Markdown Files:
Simply copy the code blocks (including the triple backticks with `mermaid`) into any Markdown file.

### In GitHub:
GitHub automatically renders Mermaid diagrams in `.md` files. Just push the file and view it.

### In VS Code:
Install extension: **Mermaid Preview** or **Markdown Preview Mermaid Support**

### Online Viewer:
1. Visit: https://mermaid.live/
2. Paste the code
3. View live preview
4. Export as PNG/SVG

### In Documentation Sites:
Most modern documentation platforms (GitBook, Docusaurus, MkDocs) support Mermaid natively.

---

## DIAGRAM LEGEND

### Flowchart Symbols:
- `[ ]` = Process/Action
- `{ }` = Decision point
- `(( ))` = Start/End
- `-->` = Flow direction
- `-.->` = Dotted connection

### Colors:
- **Green** = Success/Completed states
- **Blue** = In-progress states
- **Yellow** = Pending/Warning states
- **Red** = Rejected/Failed states
- **Orange** = Special attention (Pegawai Operasi)
- **Pink** = Admin/System level

### Icons (in text):
- 📥 = Inbox/Received
- ✅ = Approved/Completed
- ❌ = Rejected
- ⏳ = Pending
- ⚙️ = Processing
- 🚔 = Police/Operations
- 🚨 = Active operation
- 🚫 = Blocked
- 🔔 = Notification
- 📱 = SMS
- 📧 = Email
- 🆕 = New feature (v2.0)

---

**Document Version:** 2.0  
**Created:** 16 February 2026  
**For:** Jabatan Imigresen Malaysia  
**Classification:** Internal Use - Documentation  

For complete workflow details, see: `WORKFLOW_LENGKAP.md`
