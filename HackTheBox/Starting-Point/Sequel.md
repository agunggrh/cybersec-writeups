# Write-up: Sequel (Hack The Box - Starting Point)

## Ringkasan

Dokumen ini merupakan catatan penetrasi (write-up) untuk modul Sequel di platform Hack The Box (HTB). Lab ini dirancang untuk memberikan pemahaman dasar mengenai interaksi dengan database relasional (MariaDB/MySQL), manajemen koneksi, penggunaan perintah dasar SQL, serta cara bernavigasi untuk mengambil informasi sensitif atau flag dari dalam database.

## Tools & Lingkungan Kerja

- **Target IP:** `<IP_Target>`  (Mesin Sequel HTB)
- **Tools Utama:**
  - **MySQL Client (`mysql`)** (Command-line interface untuk berinteraksi dengan MariaDB/MySQL)

## Konsep Utama & Teori Dasar

### 1. Interaksi Database & Autentikasi
Memahami cara terhubung ke server database jarak jauh menggunakan kredensial tertentu (seperti user `root`) serta mengatasi kendala koneksi seperti pengaturan mode SSL/TLS (`--ssl-mode=DISABLED`).

### 2. Perintah Dasar SQL
- **`SHOW DATABASES;`**: Menampilkan daftar database yang tersedia di dalam instance server.
- **`USE nama_database;`**: Memilih database tertentu untuk mulai berinteraksi.
- **`SHOW TABLES;`** / **`DESCRIBE nama_tabel;`**: Melihat struktur tabel dan kolom yang ada di dalam database.
- **`SELECT * FROM nama_tabel;`**: Menampilkan seluruh isi data (menggunakan simbol `*` untuk semua kolom) dari sebuah tabel, diakhiri dengan titik koma (`;`).

## Langkah-langkah Penetrasi (Step-by-Step)

### Tahap 1: Koneksi ke MariaDB Instance

Melakukan koneksi ke database target menggunakan *command-line client* dengan menyesuaikan parameter koneksi (karena SSL dinonaktifkan di server target):
```bash
mysql -h `<IP_Target>`  -u root 
```
### Tahap 2: Enumerasi Database
Setelah berhasil masuk sebagai user root, lakukan pengecekan database yang tersedia:

```bash
SHOW DATABASES;
```
**Hasil: Ditemukan tiga database standar (information_schema, mysql, performance_schema) serta satu database unik milik target yaitu htb.**

### Tahap 3: Pemilihan Database & Navigasi Tabel
Pilih database htb untuk melihat isi tabel di dalamnya:

```bash
USE htb;
SHOW TABLES;
```
**Hasil: Ditemukan tabel bernama config.**

### Tahap 4: Pengambilan Flag / Penyelesaian Task
Periksa struktur dan isi dari tabel config untuk menemukan nilai flag yang dicari:

```bash
DESCRIBE config;
SELECT * FROM config;
```
**Hasil: Menemukan baris data yang berisi nilai flag pada salah satu kolomnya.**

### Kesimpulan & Rekomendasi Keamanan (Remediation)
* Batasi Akses Jaringan: Pastikan port database (seperti 3306) tidak terekspos secara publik ke internet tanpa pengamanan yang memadai (misalnya menggunakan VPN atau firewall).
* Amankan Kredensial Root: Jangan menggunakan akun root default tanpa password yang kuat untuk koneksi jarak jauh.
* Enkripsi Koneksi (SSL/TLS): Selalu aktifkan enkripsi SSL/TLS pada server database di lingkungan produksi untuk mencegah penyadapan data di jalur jaringan.**
