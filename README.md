# Sistem Klaim Sertifikat HMPTI

Aplikasi web (Laravel 11) untuk mengelola penerbitan sertifikat kegiatan HMPTI: peserta mengklaim sertifikat, panitia/admin memverifikasi, lalu sertifikat PDF dibuat otomatis, dikirim lewat email, dan bisa diverifikasi keasliannya.

## Kenapa dipakai di HMPTI

| Kebutuhan | Fitur |
|---|---|
| Banyak kegiatan (seminar, workshop, lomba, dll.) | Satu sistem, banyak event, template & penomoran sertifikat per event |
| Panitia tidak perlu kirim sertifikat satu-satu | Bulk approve, generate otomatis, email otomatis, kirim massal via import Excel |
| Mencegah klaim palsu | Wajib login, bukti kehadiran & bukti pembayaran (opsional per event), anti-duplikat, batas waktu klaim |
| Sertifikat harus bisa dicek keasliannya | Halaman verifikasi berdasarkan nomor sertifikat |
| Pergantian kepengurusan | Akun admin bertingkat (superadmin / admin), aktif/nonaktif, log aktivitas |
| Arsip | Upload otomatis ke Google Drive per folder event, unduh semua sertifikat event sebagai ZIP |

## Fitur

**Peserta**
- Lihat daftar event aktif, klaim sertifikat lewat `/claim-certificate/{slug}` (wajib login)
- Lacak status klaim, unduh sertifikat (PDF), dashboard "Sertifikat Saya"
- Verifikasi sertifikat lewat nomor sertifikat (`/verify`)
- Lupa password via email

**Admin / Panitia**
- Login + OTP email (2FA)
- Dashboard ringkasan: pending, generated, rejected, event
- Kelola event: template sertifikat, posisi overlay nama & peran, poster, prefix nomor, batas klaim, bukti kehadiran/pembayaran
- Review klaim: approve / reject (alasan cepat), bulk approve/reject, izinkan klaim ulang
- Regenerate sertifikat, kirim ulang email, pesan email kustom
- Import Excel + kirim manual, quick-generate per event, ekspor CSV, unduh ZIP
- Pencarian global dan log aktivitas admin
- Manajemen user (khusus superadmin)

## Alur singkat

```
Peserta klaim → PENDING → (admin approve) → GENERATED → email terkirim
                    └──── (admin reject) → REJECTED → (opsional) reset ke PENDING
```

Detail lengkap: [Panduan Admin](docs/PANDUAN-ADMIN.md) · [Arsitektur](docs/ARSITEKTUR.md) · [Deployment](docs/DEPLOYMENT.md) · [Kontribusi](docs/KONTRIBUSI.md)

## Kebutuhan sistem

- PHP >= 8.2, Composer
- MySQL/MariaDB (atau SQLite untuk development)
- Node.js >= 18
- SMTP untuk email (OTP & notifikasi)
- Opsional: Google Cloud service account untuk integrasi Google Drive

## Instalasi cepat

```bash
git clone https://github.com/Kepin17/claim-certif-.git
cd claim-certif-
composer install && npm install
cp .env.example .env
php artisan key:generate
# isi DB_* dan MAIL_* di .env
php artisan migrate
php artisan storage:link
npm run build
php artisan serve
php artisan queue:work   # terminal terpisah, wajib untuk generate PDF & email
```

Buat akun admin pertama (jangan memakai password bawaan seeder di production):

```bash
php artisan tinker
```
```php
\App\Models\User::create(['name' => 'Admin HMPTI', 'email' => 'admin@example.com', 'password' => bcrypt('ganti-password-ini')]);
```

> Role superadmin dan status aktif diatur lewat kolom `role` dan `is_active` pada tabel `users` (lihat [Arsitektur](docs/ARSITEKTUR.md)).

## Konfigurasi penting (`.env`)

| Variabel | Fungsi |
|---|---|
| `APP_NAME`, `APP_URL` | Nama & URL aplikasi (dipakai di email & link) |
| `DB_*` | Koneksi database |
| `MAIL_*` | SMTP untuk OTP, approval, reject, reset password |
| `QUEUE_CONNECTION=database` | Antrian generate PDF & email |
| `GOOGLE_DRIVE_ENABLED`, `GOOGLE_DRIVE_ROOT_FOLDER_ID`, `GOOGLE_SERVICE_ACCOUNT_JSON` | Upload otomatis ke Google Drive |

## Pengujian

```bash
php artisan test
```

## Lisensi

MIT.

## Kredit

- Dikembangkan oleh [Kepin17](https://github.com/Kepin17)
- Dibangun dengan Laravel 11
