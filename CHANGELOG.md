# 📋 BARCOMNET BILLING - CHANGELOG

All notable changes to this project will be documented in this file.

---

## 🛡️ [v1.0.2-SECURITY] - 2026-08-04

### 🔒 Security Hardening (Keamanan Level Produksi)
- **Bcrypt Password Hashing**: Mengimplementasikan `bcrypt` (cost factor 12) untuk password Admin, Kasir, dan Kolektor. Mendukung *progressive migration* (password lama otomatis terenkripsi).
- **Helmet.js Security Headers**: Mengaktifkan 15+ HTTP security headers (`X-Frame-Options`, `X-Content-Type-Options`, `HSTS`, `X-XSS-Protection`) dengan Referrer-Policy `same-origin`.
- **Brute-Force & Rate Limiting Protection**: 
  - Mengamankan `/admin/login` dari serangan brute-force (max 10 percobaan per 15 menit per IP).
  - Mengkonfigurasi Global Rate Limiter (1500 req/15 min) dengan pengecualian otomatis untuk aset statis (`CSS`, `JS`, `Gambar`, `Uploads`).
- **Production Mode Hardening**: Mengaktifkan `NODE_ENV=production` untuk menyembunyikan error stack traces & detail sensitif dari pengguna publik.
- **Randomized Secrets**: Meng-generate `session_secret` (48-byte random hex) dan `admin_api_key` unik.

---

### 🐛 Bug Fixes & System Stability
- **Fix Session Redirect Loop**: Memperbaiki issue `cookie_secure` yang menyebabkan session cookie ditolak oleh browser saat diakses via HTTP IP lokal (`http://192.168.98.x:3001`).
- **Robust CSRF & Referer Validation**: Memperbaiki error `403 Forbidden - Invalid Referer/Origin Format` agar navigasi dashboard berjalan lancar tanpa false-positive blocking.
- **OLT SNMP Walk Optimization**: Mengoptimalkan pengambilan data status & metrik OLT (TX/RX/Jarak ONU) dengan `Promise.all` paralel untuk penanganan koneksi cepat.
- **Flexible Admin Login**: Mendukung autentikasi menggunakan username `admin` maupun `mbahbar`.

---

## 🚀 [v1.0.1-B1] - Initial Release
- Rilis awal aplikasi billing RTRW Net berbasis Node.js, Express.js, & SQLite.
- Integrasi MikroTik API, GenieACS (TR-069), Payment Gateway (Tripay, Midtrans), & Bot WhatsApp.
