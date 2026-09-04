# Write-up: Appointment (Hack The Box - Starting Point)

## Ringkasan

Dokumen ini merupakan catatan penetrasi (write-up) resmi untuk modul Appointment di platform Hack The Box (HTB). Lab pengantar ini dirancang untuk memberikan pemahaman dasar mengenai kerentanan web populer yaitu SQL Injection (SQLi) pada halaman login, penggunaan alat pemindai jaringan seperti Nmap, pemahaman terminologi direktori web, serta teknik eksploitasi bypass autentikasi sederhana tanpa kata sandi yang sah.

## Tools & Lingkungan Kerja

- **Target IP:** `<IP_Target>` (Mesin Appointment HTB)
- **Tools Utama:**
  - **Nmap** (Port Scanner & Service Version Detection)
  - **Browser Web** (Firefox / Chrome untuk mengakses aplikasi target)

## Konsep Utama & Teori Dasar

### 1. SQL Injection (SQLi)
Kerentanan keamanan web di mana penyerang dapat memanipulasi kueri SQL yang dijalankan oleh aplikasi dengan menyisipkan input berbahaya. Hal ini memungkinkan penyerang untuk mem-bypass autentikasi, melihat, memodifikasi, atau menghapus data dalam database tanpa izin.

### 2. Komentar MySQL (`#`)
Karakter khusus dalam sintaks MySQL yang digunakan untuk mengabaikan atau membuat komentar pada sisa baris kode SQL. Dalam serangan SQLi, karakter ini sering dimanfaatkan untuk memotong kueri validasi password sehingga sistem hanya memeriksa username saja.

### 3. Direktori Web (Directory)
Istilah dalam arsitektur aplikasi web yang merujuk pada folder atau jalur penyimpanan file di dalam server web yang dapat diakses melalui URL tertentu.

## Langkah-langkah Penetrasi (Step-by-Step)

### Tahap 1: Pengumpulan Informasi & Pemindaian Port (Nmap)

Pengujian diawali dengan memindai port terbuka dan identifikasi layanan pada mesin target menggunakan Nmap:
```bash
nmap -sV <IP_Target>
```

**Hasil Pemindaian:**
- Port **80/tcp** terbuka menjalankan layanan `apache httpd 2.4.38 ((Debian))`.

### Tahap 2: Akses & Enumerasi Web Target

Buka browser web dan akses alamat IP target menggunakan protokol HTTP:
```text
http://<IP_Target>
```

Dari penelusuran web, ditemukan halaman utama yang mengarahkan ke form sistem login *appointment*.

### Tahap 3: Eksploitasi SQL Injection (Bypass Login)

Karena kolom login rentan terhadap SQL Injection, lakukan bypass autentikasi dengan memasukkan payload pada form login:
- **Username:** `admin'#`
- **Password:** *(isi dengan sembarang karakter, misal: `password`)*

**Penjelasan:** Tanda petik tunggal (`'`) menutup string query SQL asli, dan karakter `#` mengomentari sisa perintah pengecekan password di belakangnya, sehingga database menganggap login valid sebagai user admin.

### Tahap 4: Pengambilan Flag / Penyelesaian Task

Setelah menekan tombol Sign In, sistem berhasil melakukan bypass dan mengarahkan pengguna ke halaman dashboard utama yang menampilkan pesan sukses beserta flag/informasi yang dicari.

## Kesimpulan & Rekomendasi Keamanan (Remediation)

1. **Gunakan Prepared Statements (Parameterized Queries):** Pastikan aplikasi web memisahkan kode SQL dari data input pengguna untuk mencegah interpretasi langsung input sebagai perintah database.
2. **Validasi dan Sanitasi Input:** Terapkan penyaringan ketat terhadap karakter khusus (seperti `'`, `"`, `#`, `--`) pada semua data input yang diterima dari pengguna.
3. **Terapkan Prinsip Hak Istimewa Terendah (Principle of Least Privilege):** Batasi hak akses akun database yang terhubung ke aplikasi web agar tidak memiliki kontrol penuh yang berlebihan.
