# Panduan Admin / Panitia HMPTI

## 1. Login
1. Buka `/login`, masukkan email & password.
2. Masukkan kode OTP 6 digit yang dikirim ke email (berlaku 10 menit). Gunakan `/otp/resend` bila belum masuk.
3. Lupa password: `/forgot-password`.

## 2. Menyiapkan event
Menu **Events → Create**:
- **Nama, slug, deskripsi, tanggal, lokasi, poster**
- **Template sertifikat** (PDF, ukuran A4) + posisi overlay nama (top/left/size/color) dan peran (teks, posisi, ukuran, warna)
- **Prefix nomor sertifikat** (mis. `HMPTI/SEMINAR`) agar nomor mudah dilacak
- **Batas waktu klaim** — form klaim tutup otomatis setelah deadline
- **Wajib bukti kehadiran / bukti pembayaran** — peserta harus mengunggah foto saat klaim
- Aktif/nonaktif lewat tombol toggle; event yang dihapus memakai soft delete

Tips: cek posisi nama dengan **Preview** sebelum membuka klaim ke peserta.

## 3. Memproses klaim
| Menu | Tindakan |
|---|---|
| **Pending → pilih event** | Tinjau bukti, **Approve** / **Reject** (pilih alasan cepat). Centang beberapa klaim untuk bulk action |
| **Rejected → pilih event** | Lihat alasan; **Allow Re-claim** mengembalikan ke pending dan memberi tahu peserta via email |
| **Generated → pilih event** | Regenerate PDF, kirim ulang email, **Export CSV**, unduh semua sertifikat (ZIP) |

Approve memicu job antrian: PDF dibuat → (opsional) diunggah ke Google Drive → email dikirim. **Pastikan `queue:work` berjalan**, kalau tidak klaim tidak akan berpindah ke generated.

## 4. Kirim tanpa klaim peserta
- **Import Excel**: unduh template (`/admin/certificate/template`), isi data peserta, upload.
- **Manual send**: kirim sertifikat untuk satu peserta.
- **Quick generate**: buat sertifikat per event secara cepat.

## 5. Audit & pencarian
- Kolom pencarian di navbar: cari nama, email, nomor sertifikat, atau event.
- **Activity Log**: riwayat approve, reject, reset, regenerate, dsb. beserta admin pelaku.

## 6. Manajemen user (superadmin)
Menu **Users**: tambah, edit, hapus, aktif/nonaktifkan akun. Saat serah terima kepengurusan, nonaktifkan akun pengurus lama dan buat akun baru — jangan berbagi password.

## 7. Verifikasi oleh pihak ketiga
Pihak luar dapat mengecek keaslian di `/verify?number=<nomor sertifikat>`.
