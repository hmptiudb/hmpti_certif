# Arsitektur

Laravel 11, Blade, Vite. PDF dengan `barryvdh/laravel-dompdf`, 2FA dengan `pragmarx/google2fa-laravel`, Google Drive dengan `google/apiclient`.

## Struktur utama

| Lokasi | Isi |
|---|---|
| `routes/web.php` | Semua route (publik, peserta login, `/admin`, OTP, reset password) |
| `app/Http/Controllers/CertificateController.php` | Alur peserta: daftar event, klaim, tracking, download, verifikasi |
| `app/Http/Controllers/Admin/` | `AuthController` (login/OTP/profil), `CertificateAdminController` (review, bulk, import, ekspor), `EventController`, `UserController` |
| `app/Http/Middleware/` | `CheckOTPVerification` (alias `otp.verified`), `SuperAdmin` (alias `superadmin`) |
| `app/Services/CertificateService.php` | Pembuatan PDF & nomor sertifikat |
| `app/Services/GoogleDriveService.php` | Upload ke folder event di Google Drive |
| `app/Jobs/GenerateCertificate*.php` | Job antrian generate PDF |
| `app/Mail/` | Email approved, rejected, reset-to-pending |
| `app/Models/` | `User`, `Event`, `Certificate`, `CertificateType`, `AdminActivityLog` |
| `resources/views/` | `admin/`, `certificates/`, `emails/`, `errors/`, `layouts/` |
| `database/migrations/` | Skema; lihat daftar tabel di bawah |
| `ecosystem.config.js` | Konfigurasi PM2 untuk queue worker |
| `public/templates/template_sertif.csv` | Template import Excel/CSV |

## Data

- **users** — `role`, `is_active`, kolom OTP
- **events** — slug, template, overlay, `certificate_number_prefix`, `claim_deadline`, `requires_attendance_proof`, `requires_payment_proof`, soft delete
- **certificates** — data peserta, status, `unique_key`, bukti kehadiran/pembayaran, `custom_email_message`, `google_drive_file_id`
- **certificate_types**, **admin_activity_logs**

## Alur klaim

1. Peserta login → `POST /claim-certificate` (throttle 5/menit, cek `Event::isClaimOpen()` dan duplikat)
2. Admin approve → job `GenerateCertificate` di-queue
3. Job membuat PDF (DOMPDF), opsional unggah ke Drive, kirim `CertificateApproved`
4. Peserta unduh via `/download-certificate` atau verifikasi via `/verify`

## Keamanan

OTP email untuk admin, throttle pada route sensitif, CSRF, CORS dibatasi (`config/cors.php`), hash password bcrypt, role superadmin untuk manajemen user, log aktivitas admin.
