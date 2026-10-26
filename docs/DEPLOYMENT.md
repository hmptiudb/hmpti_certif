# Deployment

## Checklist production
```bash
composer install --no-dev --optimize-autoloader
npm ci && npm run build
cp .env.example .env && php artisan key:generate
# set APP_ENV=production, APP_DEBUG=false, APP_URL, DB_*, MAIL_*
php artisan migrate --force
php artisan storage:link
php artisan config:cache && php artisan route:cache && php artisan view:cache
```
Arahkan document root web server ke `public/`.

## Queue worker (wajib)
Generate PDF dan pengiriman email berjalan di antrian. Dengan PM2:
```bash
pm2 start ecosystem.config.js
pm2 save
```
Setelah deploy kode baru: `pm2 restart certif-queue`.

## Google Drive (opsional)
1. Buat service account di Google Cloud, aktifkan Drive API, unduh JSON key.
2. Simpan di `storage/app/google-service-account.json` (jangan di-commit).
3. Bagikan folder root Drive ke email service account (akses Editor).
4. Isi `.env`: `GOOGLE_DRIVE_ENABLED=true`, `GOOGLE_DRIVE_ROOT_FOLDER_ID=<id folder>`.

## Keamanan
- `AdminUserSeeder` berisi akun bawaan — **jangan dijalankan di production** atau ganti kredensialnya dan jangan commit password.
- Batasi `allowed_origins` di `config/cors.php` ke domain sendiri.
- Backup database dan folder `storage/app` secara berkala.
