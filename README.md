# ⚡ WattCare — Aplikasi Monitoring Konsumsi Listrik

**WattCare** adalah aplikasi web berbasis Laravel untuk memantau dan menganalisis penggunaan listrik rumah tangga. Aplikasi ini membantu pengguna mencatat penggunaan perangkat elektronik, menghitung konsumsi energi (kWh), estimasi biaya, serta dampak lingkungan (emisi CO₂) secara otomatis, disajikan dalam bentuk dashboard dan grafik yang mudah dipahami.

---

## 📋 Daftar Isi

- [Fitur Utama](#-fitur-utama)
- [Tech Stack](#-tech-stack)
- [Arsitektur Sistem](#-arsitektur-sistem)
- [Struktur Proyek](#-struktur-proyek)
- [Alur Kerja Aplikasi](#-alur-kerja-aplikasi)
- [Database Schema](#-database-schema)
- [API Endpoints](#-api-endpoints)
- [Halaman & Routing](#-halaman--routing)
- [Panduan Instalasi](#-panduan-instalasi)

---

## ✨ Fitur Utama

| Kode | Fitur | Deskripsi |
|------|-------|-----------|
| F-01 | Registrasi Pengguna | Pengguna baru membuat akun (nama, email, password, daya listrik) |
| F-02 | Login & Logout | Autentikasi pengguna untuk mengakses sistem |
| F-03 | Input Data Perangkat | Form input nama alat, daya (watt), jam pemakaian, jumlah unit |
| F-04 | Validasi Data Input | Validasi angka positif, semua field wajib diisi |
| F-05 | Kalkulasi kWh Harian | Rumus: (Daya × Jam × Jumlah Unit) ÷ 1000 |
| F-06 | Kalkulasi Estimasi Biaya | Total kWh × Tarif PLN (Rp/kWh) |
| F-07 | Kalkulasi Emisi CO₂ | Total kWh × 0,89 kg CO₂ |
| F-08 | Dashboard Monitoring | Tampilan total penggunaan, estimasi biaya, emisi CO₂, dan grafik 7 hari |
| F-09 | Notifikasi Lonjakan | Peringatan ketika penggunaan listrik minggu ini lebih tinggi dari biasanya |
| F-10 | Riwayat Analisis | Histori semua pencatatan per pengguna |
| F-11 | Filter Riwayat | Filter berdasarkan rentang tanggal |
| F-12 | Manajemen Perangkat | Tambah, ubah, hapus data perangkat elektronik |
| F-13 | Rekomendasi Hemat Energi | Tips & saran penghematan berdasarkan pola penggunaan |
| F-14 | Manajemen Profil | Edit nama, email, password, daya listrik |

---

## 🛠 Tech Stack

| Layer | Teknologi |
|-------|-----------|
| **Frontend** | Blade Templates (Laravel), HTML5, CSS3, JavaScript, Chart.js (grafik), TailwindCSS / Bootstrap |
| **Backend** | Laravel (PHP Framework), REST API |
| **Web Server** | XAMPP (Apache) |
| **Database** | MySQL |
| **Authentication** | Laravel Breeze / Jetstream (Session-based) |
| **Development Tools** | Visual Studio Code, Postman (API testing) |
| **Deployment** | Local Server (XAMPP) |

---

## 🏗 Arsitektur Sistem

WattCare menggunakan **Laravel Monolithic Architecture** dengan struktur MVC (Model-View-Controller). Sistem hanya memiliki **satu role pengguna (User)** tanpa role Admin atau Guest terpisah.

```
┌─────────────────────────────────────────────────────────────┐
│                      BROWSER (Client)                       │
│  ┌───────────────────────────────────────────────────────┐  │
│  │           Blade Templates + CSS + JavaScript          │  │
│  │  ┌────────┐ ┌────────┐ ┌────────┐ ┌──────────────┐  │  │
│  │  │ Login  │ │Register│ │Dashboard│ │  Riwayat    │  │  │
│  │  │ Page   │ │ Page   │ │  Page   │ │    Page      │  │  │
│  │  └────────┘ └────────┘ └────────┘ └──────────────┘  │  │
│  │  ┌────────┐ ┌────────────────────────────────────┐  │  │
│  │  │Profil  │ │        Rekomendasi Page            │  │  │
│  │  │Page    │ │                                    │  │  │
│  │  └────────┘ └────────────────────────────────────┘  │  │
│  └─────────────────────┬───────────────────────────────┘  │
└────────────────────────┼───────────────────────────────────┘
                         │ HTTP (Request/Response + Session)
┌────────────────────────┼───────────────────────────────────┐
│              LARAVEL APPLICATION (Monolith)                │
│  ┌─────────────────────┴───────────────────────────────┐  │
│  │                   ROUTES (web.php)                  │  │
│  └─────────────────────┬───────────────────────────────┘  │
│                         │                                  │
│  ┌──────────────────────┴──────────────────────────────┐  │
│  │                   CONTROLLERS                        │  │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────┐ │  │
│  │  │ Auth     │ │ Device   │ │Analysis  │ │Recommend│ │  │
│  │  │Controller│ │Controller│ │Controller│ │Controller│ │  │
│  │  └────┬─────┘ └────┬─────┘ └────┬─────┘ └───┬────┘ │  │
│  └───────┼────────────┼────────────┼───────────┼───────┘  │
│          │            │            │           │          │
│  ┌───────┴────────────┴────────────┴───────────┴───────┐  │
│  │                   MIDDLEWARE                        │  │
│  │                    (auth)                           │  │
│  └─────────────────────┬───────────────────────────────┘  │
│                        │                                   │
│  ┌─────────────────────┴───────────────────────────────┐  │
│  │              BUSINESS LOGIC (Services)              │  │
│  │  ┌─────────────────────────────────────────────┐   │  │
│  │  │ • CalculatorService (kWh, biaya, CO₂)       │   │  │
│  │  │ • ValidationService (validasi input)        │   │  │
│  │  │ • AlertService (notifikasi lonjakan)        │   │  │
│  │  │ • RecommendationService (tips penghematan)  │   │  │
│  │  └─────────────────────────────────────────────┘   │  │
│  └─────────────────────┬───────────────────────────────┘  │
│                        │                                   │
│  ┌─────────────────────┴───────────────────────────────┐  │
│  │              ELOQUENT ORM (Models)                  │  │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────┐ │  │
│  │  │  User    │ │  Device  │ │Monitoring│ │Billing │ │  │
│  │  │  Model   │ │  Model   │ │   Log    │ │ Model  │ │  │
│  │  └──────────┘ └──────────┘ └──────────┘ └────────┘ │  │
│  │  ┌──────────┐ ┌──────────────────────────────────┐ │  │
│  │  │ CO2Impact│ │    Recommendation Model          │ │  │
│  │  │  Model   │ │                                  │ │  │
│  │  └──────────┘ └──────────────────────────────────┘ │  │
│  └─────────────────────┬───────────────────────────────┘  │
└────────────────────────┼───────────────────────────────────┘
                         │
┌────────────────────────┼───────────────────────────────────┐
│                   DATA TIER                                │
│  ┌─────────────────────┴───────────────────────────────┐  │
│  │                   MySQL Database                    │  │
│  │  ┌─────────┐ ┌─────────┐ ┌──────────────┐         │  │
│  │  │ users   │ │ devices │ │ monitoring_  │         │  │
│  │  │         │ │         │ │    logs      │         │  │
│  │  └─────────┘ └─────────┘ └──────────────┘         │  │
│  │  ┌─────────┐ ┌─────────┐ ┌──────────────┐         │  │
│  │  │ billing │ │co2_impact│ │recommendation│         │  │
│  │  └─────────┘ └─────────┘ └──────────────┘         │  │
│  └─────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────┘
```

### Penjelasan Arsitektur:

| Komponen | Teknologi | Fungsi |
|----------|-----------|--------|
| **View Layer** | Blade Templates, HTML5, CSS3, JavaScript, Chart.js, TailwindCSS/Bootstrap | Antarmuka pengguna, menampilkan dashboard & grafik |
| **Controller Layer** | Laravel Controllers | Menangani request HTTP, memanggil service |
| **Middleware** | Laravel Middleware (auth) | Memastikan hanya pengguna terautentikasi yang dapat mengakses |
| **Business Logic** | Service Classes | Perhitungan kWh/biaya/CO₂, validasi, notifikasi lonjakan |
| **ORM** | Eloquent | Interaksi dengan database MySQL |
| **Web Server** | XAMPP (Apache) | Menjalankan aplikasi Laravel |

---

## 📁 Struktur Proyek (Laravel)

```
WATTCARE-LARAVEL/
│
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── Auth/
│   │   │   │   ├── LoginController.php
│   │   │   │   └── RegisterController.php
│   │   │   ├── DashboardController.php
│   │   │   ├── DeviceController.php
│   │   │   ├── AnalysisController.php
│   │   │   ├── HistoryController.php
│   │   │   ├── RecommendationController.php
│   │   │   └── ProfileController.php
│   │   │
│   │   ├── Middleware/
│   │   │   └── Authenticate.php
│   │   │
│   │   └── Requests/
│   │       ├── DeviceRequest.php
│   │       ├── AnalysisRequest.php
│   │       └── ProfileUpdateRequest.php
│   │
│   ├── Models/
│   │   ├── User.php
│   │   ├── Device.php
│   │   ├── MonitoringLog.php
│   │   ├── Billing.php
│   │   ├── CO2Impact.php
│   │   └── Recommendation.php
│   │
│   └── Services/
│       ├── CalculatorService.php      # kWh, biaya, CO₂
│       ├── AlertService.php           # Notifikasi lonjakan penggunaan
│       ├── ValidationService.php      # Validasi input
│       └── RecommendationService.php  # Tips penghematan
│
├── database/
│   ├── migrations/
│   │   ├── 2014_10_12_000000_create_users_table.php
│   │   ├── 2024_01_01_000001_create_devices_table.php
│   │   ├── 2024_01_01_000002_create_monitoring_logs_table.php
│   │   ├── 2024_01_01_000003_create_billings_table.php
│   │   ├── 2024_01_01_000004_create_co2_impacts_table.php
│   │   └── 2024_01_01_000005_create_recommendations_table.php
│   │
│   └── seeders/
│       └── RecommendationSeeder.php
│
├── resources/
│   ├── views/
│   │   ├── layouts/
│   │   │   └── app.blade.php          # Main layout with sidebar
│   │   │
│   │   ├── auth/
│   │   │   ├── login.blade.php
│   │   │   └── register.blade.php
│   │   │
│   │   ├── dashboard/
│   │   │   └── index.blade.php        # Dashboard utama
│   │   │
│   │   ├── devices/
│   │   │   ├── index.blade.php
│   │   │   ├── create.blade.php
│   │   │   └── edit.blade.php
│   │   │
│   │   ├── analysis/
│   │   │   └── input.blade.php
│   │   │
│   │   ├── history/
│   │   │   └── index.blade.php
│   │   │
│   │   ├── recommendations/
│   │   │   └── index.blade.php
│   │   │
│   │   └── profile/
│   │       └── edit.blade.php
│   │
│   └── css/
│       └── app.css
│
├── routes/
│   └── web.php                       # Semua routes web
│
├── public/
│   ├── index.php
│   ├── css/
│   └── js/
│
├── config/
│   ├── app.php
│   ├── database.php
│   └── constants.php                 # Tarif listrik, faktor CO₂
│
├── .env
├── artisan
├── composer.json
└── package.json
```

---

## 📊 Dashboard Utama (WattCare)

Dashboard adalah halaman utama setelah pengguna login. Berikut adalah tampilan dan komponen dashboard:

```
┌─────────────────────────────────────────────────────────────────────┐
│  ⚡ WattCare                                    [Profile]  [Logout]  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  📊 Dashboard                                                        │
│  Pantau penggunaan listrik Anda secara real-time                    │
│                                                                      │
│  ┌────────────────────────────────────────────────────────────────┐ │
│  │  ⚠️ Penggunaan Meningkat                                       │ │
│  │  Penggunaan listrik minggu ini lebih tinggi dari biasanya.     │ │
│  └────────────────────────────────────────────────────────────────┘ │
│                                                                      │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐                │
│  │ Total        │ │ Estimasi     │ │ Emisi CO₂    │                │
│  │ Penggunaan   │ │ Biaya        │ │              │                │
│  │              │ │              │ │              │                │
│  │ 489.00 kWh   │ │ Rp706.458,3  │ │ 415.65 kg    │                │
│  └──────────────┘ └──────────────┘ └──────────────┘                │
│                                                                      │
│  ┌──────────────┐ ┌──────────────┐                                 │
│  │ Hari Ini     │ │ Minggu Ini   │                                 │
│  │              │ │              │                                 │
│  │ 10.91 kWh    │ │ 109.10 kWh   │                                 │
│  └──────────────┘ └──────────────┘                                 │
│                                                                      │
│  📈 Grafik Penggunaan (7 Hari)                                      │
│  ┌────────────────────────────────────────────────────────────────┐ │
│  │                                                                │ │
│  │     █                                                         │ │
│  │     █ █                                                       │ │
│  │     █ █ █                                                     │ │
│  │     █ █ █ █                                                   │ │
│  │  ┌──────────────────────────────────────────────────────────┐│ │
│  │  │ 12  18  24  30  06  12  18  (X-Axis: Hari/Tanggal)      ││ │
│  │  └──────────────────────────────────────────────────────────┘│ │
│  │                                                                │ │
│  │  (Y-Axis: kWh) → 0, 2, 4, 6, 8, 10, 12                       │ │
│  └────────────────────────────────────────────────────────────────┘ │
│                                                                      │
│  💡 Tips Hemat Energi                                                │
│  • Matikan perangkat elektronik yang tidak digunakan                │
│  • Gunakan perangkat hemat energi                                   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Komponen Dashboard:

| Komponen | Deskripsi | Data Source |
|----------|-----------|-------------|
| **Alert Penggunaan Meningkat** | Notifikasi ketika penggunaan minggu ini > rata-rata | `AlertService` |
| **Total Penggunaan** | Akumulasi seluruh kWh dari semua perangkat | `monitoring_logs` |
| **Estimasi Biaya** | Total kWh × Tarif per kWh (sesuai daya pengguna) | `CalculatorService` |
| **Emisi CO₂** | Total kWh × 0,89 kg CO₂ | `CalculatorService` |
| **Hari Ini** | Total kWh untuk hari ini | `monitoring_logs` (today) |
| **Minggu Ini** | Total kWh untuk 7 hari terakhir | `monitoring_logs` (last 7 days) |
| **Grafik 7 Hari** | Visualisasi tren penggunaan per hari | `Chart.js` + data 7 hari |

---

## ⚡ Alur Kerja Aplikasi

### Langkah-langkah Proses Analisis Konsumsi Listrik

```
   USER                    LARAVEL APP                      DATABASE
    │                           │                               │
    │  1. POST /register        │                               │
    │     (name, email,         │                               │
    │      password, daya_va)   │                               │
    │ ─────────────────────────>│                               │
    │                           │  2. Validasi & simpan user    │
    │                           │ ─────────────────────────────>│
    │                           │ <─────────────────────────────│
    │  3. Redirect /login       │                               │
    │ <─────────────────────────│                               │
    │                           │                               │
    │  4. POST /login           │                               │
    │ ─────────────────────────>│                               │
    │                           │  5. Auth attempt              │
    │                           │ ─────────────────────────────>│
    │                           │ <─────────────────────────────│
    │  6. Redirect /dashboard   │                               │
    │ <─────────────────────────│                               │
    │                           │                               │
    │  7. GET /dashboard        │                               │
    │ ─────────────────────────>│                               │
    │                           │  8. Ambil data:              │
    │                           │     - Total kWh               │
    │                           │     - Total biaya             │
    │                           │     - Total CO₂               │
    │                           │     - Data grafik 7 hari      │
    │                           │ ─────────────────────────────>│
    │                           │ <─────────────────────────────│
    │                           │                               │
    │                           │  9. Cek lonjakan penggunaan   │
    │                           │     (AlertService)            │
    │                           │                               │
    │  10. Tampilkan dashboard  │                               │
    │      dengan Chart.js      │                               │
    │ <─────────────────────────│                               │
    │                           │                               │
    │  11. POST /devices        │                               │
    │      (tambah perangkat)   │                               │
    │ ─────────────────────────>│                               │
    │                           │  12. Kalkulasi via Service:   │
    │                           │      ┌─────────────────────┐  │
    │                           │      │ CalculatorService:  │  │
    │                           │      │ kWh = (Watt × Jam   │  │
    │                           │      │        × Jumlah)     │  │
    │                           │      │         ÷ 1000       │  │
    │                           │      │                     │  │
    │                           │      │ Biaya = kWh × Tarif  │  │
    │                           │      │ CO₂ = kWh × 0,89     │  │
    │                           │      └─────────────────────┘  │
    │                           │                               │
    │                           │  13. Simpan ke database       │
    │                           │ ─────────────────────────────>│
    │                           │                               │
    │  14. Redirect ke dashboard│                               │
    │ <─────────────────────────│                               │
    │                           │                               │
```

### Detail Perhitungan (CalculatorService)

#### 📊 Konsumsi kWh Harian

```php
// app/Services/CalculatorService.php

public function calculateKWh($watt, $hours, $unit)
{
    return ($watt * $hours * $unit) / 1000;
}
```

#### 💰 Estimasi Biaya Listrik

```php
public function calculateCost($totalKWh, $tariffPerKWh)
{
    return $totalKWh * $tariffPerKWh;
}
```

| Daya (VA) | Tarif per kWh (Rp) |
|-----------|---------------------|
| 450 VA | Rp415 |
| 900 VA | Rp1.352 |
| 1.300 VA | Rp1.444,70 |
| 2.200 VA | Rp1.444,70 |
| 3.500 VA+ | Rp1.699,53 |

#### 🌿 Emisi CO₂

```php
public function calculateCO2($totalKWh)
{
    $emissionFactor = 0.89; // kg CO₂ per kWh (PLN 2022)
    return $totalKWh * $emissionFactor;
}
```

#### ⚠️ Alert Lonjakan Penggunaan (AlertService)

```php
// app/Services/AlertService.php

public function checkUsageSpike($currentWeekUsage, $averageWeeklyUsage)
{
    if ($currentWeekUsage > $averageWeeklyUsage * 1.2) {
        return [
            'has_spike' => true,
            'message' => 'Penggunaan listrik minggu ini lebih tinggi dari biasanya.'
        ];
    }
    return ['has_spike' => false];
}
```

---

## 💾 Database Schema

### ERD (Entity Relationship Diagram)

```
┌─────────────────────────────────────────────────────────────────┐
│                         ERD WATTCARE                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│    ┌──────────┐          ┌──────────┐          ┌──────────┐    │
│    │   User   │ 1      N │  Device  │          │Recommend-│    │
│    │──────────│◄────────►│──────────│          │  ation   │    │
│    │ id (PK)  │          │id (PK)   │          │id (PK)   │    │
│    │ name     │          │user_id   │          │user_id   │    │
│    │ email    │          │nama_device│         │type      │    │
│    │ password │          │daya_watt │          │message   │    │
│    │ daya_va  │          │jumlah_unit│         │          │    │
│    └────┬─────┘          └──────────┘          └────┬─────┘    │
│         │                                            │          │
│         │ 1                                          │ N        │
│         │                                            │          │
│         ▼                                            ▼          │
│    ┌─────────────┐                                                   │
│    │MonitoringLog│          ┌──────────┐          ┌──────────┐    │
│    │─────────────│          │ Billing  │          │CO2Impact │    │
│    │id (PK)      │ 1      1 │id (PK)   │ 1      1 │id (PK)   │    │
│    │user_id      │◄────────►│log_id    │◄────────►│log_id    │    │
│    │date         │          │estimasi_ │          │emisi_co2 │    │
│    │total_kwh    │          │biaya     │          │faktor_   │    │
│    └─────────────┘          └──────────┘          │emisi     │    │
│                                                    └──────────┘    │
└─────────────────────────────────────────────────────────────────────┘
```

### Tabel-tabel Database

#### 1. Tabel `users`

| Kolom | Tipe | Keterangan |
|-------|------|-------------|
| id | BIGINT (PK) | ID unik pengguna |
| name | VARCHAR(100) | Nama lengkap |
| email | VARCHAR(100) | Email (unik) |
| password | VARCHAR(255) | Hash password |
| daya_va | INT | Daya listrik rumah (VA) |
| created_at | TIMESTAMP | Waktu registrasi |
| updated_at | TIMESTAMP | Waktu update |

#### 2. Tabel `devices`

| Kolom | Tipe | Keterangan |
|-------|------|-------------|
| id | BIGINT (PK) | ID unik perangkat |
| user_id | BIGINT (FK) | Referensi ke users |
| nama_device | VARCHAR(100) | Nama alat elektronik |
| daya_watt | INT | Daya dalam watt |
| jumlah_unit | INT | Jumlah unit (default 1) |
| created_at | TIMESTAMP | Waktu dibuat |
| updated_at | TIMESTAMP | Waktu update |

#### 3. Tabel `monitoring_logs`

| Kolom | Tipe | Keterangan |
|-------|------|-------------|
| id | BIGINT (PK) | ID unik log |
| user_id | BIGINT (FK) | Referensi ke users |
| device_id | BIGINT (FK) | Referensi ke devices |
| tanggal | DATE | Tanggal pencatatan |
| jam_pemakaian | DECIMAL(5,2) | Durasi pemakaian (jam) |
| total_kwh | DECIMAL(10,2) | Total kWh harian per device |
| created_at | TIMESTAMP | Waktu dibuat |

#### 4. Tabel `billings`

| Kolom | Tipe | Keterangan |
|-------|------|-------------|
| id | BIGINT (PK) | ID unik billing |
| log_id | BIGINT (FK) | Referensi ke monitoring_logs |
| estimasi_biaya | DECIMAL(12,2) | Estimasi biaya (Rp) |
| tarif_per_kwh | DECIMAL(10,2) | Tarif yang digunakan |
| created_at | TIMESTAMP | Waktu dibuat |

#### 5. Tabel `co2_impacts`

| Kolom | Tipe | Keterangan |
|-------|------|-------------|
| id | BIGINT (PK) | ID unik impact |
| log_id | BIGINT (FK) | Referensi ke monitoring_logs |
| emisi_co2 | DECIMAL(10,2) | Emisi CO₂ (kg) |
| faktor_emisi | DECIMAL(5,3) | Faktor konversi (0,89) |
| created_at | TIMESTAMP | Waktu dibuat |

#### 6. Tabel `recommendations`

| Kolom | Tipe | Keterangan |
|-------|------|-------------|
| id | BIGINT (PK) | ID unik rekomendasi |
| user_id | BIGINT (FK) | Referensi ke users |
| tipe | VARCHAR(50) | Tipe rekomendasi |
| pesan | TEXT | Isi rekomendasi |
| created_at | TIMESTAMP | Waktu dibuat |

### Eloquent Relationships

```php
// app/Models/User.php
class User extends Authenticatable
{
    public function devices()
    {
        return $this->hasMany(Device::class);
    }
    
    public function monitoringLogs()
    {
        return $this->hasMany(MonitoringLog::class);
    }
    
    public function recommendations()
    {
        return $this->hasMany(Recommendation::class);
    }
    
    public function getTariffPerKWhAttribute()
    {
        $tariffs = [
            450 => 415,
            900 => 1352,
            1300 => 1444.70,
            2200 => 1444.70,
            3500 => 1699.53,
        ];
        
        return $tariffs[$this->daya_va] ?? 1444.70;
    }
}
```

---

## 🔌 Routes (Laravel)

### Web Routes (`routes/web.php`)

```php
<?php

use App\Http\Controllers\{
    Auth\LoginController,
    Auth\RegisterController,
    DashboardController,
    DeviceController,
    AnalysisController,
    HistoryController,
    RecommendationController,
    ProfileController
};

// Guest routes (hanya untuk yang belum login)
Route::middleware('guest')->group(function () {
    Route::get('/login', [LoginController::class, 'showLoginForm'])->name('login');
    Route::post('/login', [LoginController::class, 'login']);
    Route::get('/register', [RegisterController::class, 'showRegistrationForm'])->name('register');
    Route::post('/register', [RegisterController::class, 'register']);
});

// Auth routes (hanya untuk yang sudah login)
Route::middleware('auth')->group(function () {
    Route::post('/logout', [LoginController::class, 'logout'])->name('logout');
    
    // Dashboard
    Route::get('/dashboard', [DashboardController::class, 'index'])->name('dashboard');
    
    // Devices
    Route::resource('devices', DeviceController::class);
    
    // Analysis
    Route::get('/analysis/input', [AnalysisController::class, 'create'])->name('analysis.input');
    Route::post('/analysis', [AnalysisController::class, 'store'])->name('analysis.store');
    
    // History
    Route::get('/history', [HistoryController::class, 'index'])->name('history.index');
    
    // Recommendations
    Route::get('/recommendations', [RecommendationController::class, 'index'])->name('recommendations.index');
    
    // Profile
    Route::get('/profile', [ProfileController::class, 'edit'])->name('profile.edit');
    Route::put('/profile', [ProfileController::class, 'update'])->name('profile.update');
});

// Landing page (bisa diakses semua)
Route::get('/', function () {
    return view('landing');
})->name('landing');
```

---

## 🗺 Halaman & Routing

| Path | View | Middleware | Fungsi (KF) |
|------|------|------------|--------------|
| `/` | `landing.blade.php` | none | Landing page / Homepage |
| `/login` | `auth.login.blade.php` | guest | F-02 |
| `/register` | `auth.register.blade.php` | guest | F-01 |
| `/dashboard` | `dashboard.index.blade.php` | auth | F-08, F-09 |
| `/devices` | `devices.index.blade.php` | auth | F-12 |
| `/devices/create` | `devices.create.blade.php` | auth | F-03, F-04 |
| `/analysis/input` | `analysis.input.blade.php` | auth | F-03, F-04, F-05 |
| `/history` | `history.index.blade.php` | auth | F-10, F-11 |
| `/recommendations` | `recommendations.index.blade.php` | auth | F-13 |
| `/profile` | `profile.edit.blade.php` | auth | F-14 |

### Layout System

```php
// resources/views/layouts/app.blade.php
<!DOCTYPE html>
<html>
<head>
    <title>WattCare - @yield('title')</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="csrf-token" content="{{ csrf_token() }}">
    
    <!-- TailwindCSS / Bootstrap -->
    @vite(['resources/css/app.css', 'resources/js/app.js'])
    
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body>
    <div class="app-container">
        @include('partials.sidebar')
        
        <main class="main-content">
            @if(session('success'))
                <div class="alert alert-success">{{ session('success') }}</div>
            @endif
            
            @if(session('error'))
                <div class="alert alert-danger">{{ session('error') }}</div>
            @endif
            
            @yield('content')
        </main>
    </div>
    
    @stack('scripts')
</body>
</html>
```

---

## 🚀 Panduan Instalasi (Laravel + XAMPP)

### Prerequisites
- PHP 8.1+ (tersedia di XAMPP)
- Composer
- MySQL (tersedia di XAMPP)
- Node.js & NPM (untuk frontend assets)
- XAMPP (Apache + MySQL)

### 1. Clone Repository
```bash
git clone <repo-url>
cd wattcare-laravel
```

### 2. Install Dependencies
```bash
composer install
npm install
```

### 3. Environment Configuration
```bash
cp .env.example .env
php artisan key:generate
```

### 4. Konfigurasi Database (.env)
```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=wattcare_db
DB_USERNAME=root
DB_PASSWORD=
```

### 5. Setup XAMPP
```bash
# Nyalakan Apache dan MySQL di XAMPP Control Panel
# Buat database di phpMyAdmin
http://localhost/phpmyadmin
CREATE DATABASE wattcare_db;
```

### 6. Jalankan Migration & Seeder
```bash
php artisan migrate --seed
```

### 7. Compile Frontend Assets
```bash
npm run build
# atau untuk development
npm run dev
```

### 8. Jalankan Aplikasi
```bash
php artisan serve
# → http://localhost:8000
```

Atau akses langsung via XAMPP:
```
http://localhost/wattcare-laravel/public
```

---

## 📄 Artisan Commands yang Digunakan

```bash
# Membuat Model dengan Migration
php artisan make:model Device -m

# Membuat Controller
php artisan make:controller DeviceController --resource
php artisan make:controller DashboardController
php artisan make:controller HistoryController

# Membuat Request Validation
php artisan make:request DeviceRequest
php artisan make:request AnalysisRequest

# Membuat Seeder
php artisan make:seeder RecommendationSeeder

# Menjalankan migration
php artisan migrate:fresh --seed

# Melihat daftar routes
php artisan route:list

# Install Laravel Breeze
composer require laravel/breeze --dev
php artisan breeze:install blade
npm install
npm run dev
```

---

## 📄 Catatan Penting

> **Role Pengguna:** Sistem hanya memiliki **satu role (User)**. Tidak ada role Admin atau Guest terpisah.

> **Laravel Version:** Menggunakan Laravel 10 atau 11

> **Authentication:** Menggunakan Laravel Breeze dengan Blade stack (session-based)

> **Frontend:** Menggunakan TailwindCSS/Bootstrap + Chart.js untuk grafik

> **Web Server:** Aplikasi berjalan di XAMPP (Apache) dengan PHP 8.1+

> **Tarif Listrik:** Berdasarkan daya (VA) yang dipilih saat registrasi

> **Faktor Emisi:** 0,89 kg CO₂/kWh (data PLN 2022)

> **Notifikasi Lonjakan:** Muncul di dashboard ketika penggunaan minggu ini > 20% dari rata-rata

---

## 📄 Lisensi

Proyek Tugas Besar — Kelompok C

---
