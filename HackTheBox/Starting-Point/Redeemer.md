# Write-up: Redeemer (Hack The Box - Starting Point)

---

##  Ringkasan
Dokumen ini merupakan catatan penetrasi (*write-up*) untuk lab **Redeemer** di platform **Hack The Box (Starting Point)**. Lab ini dirancang untuk memperkenalkan konsep dasar interaksi dengan database *in-memory* **Redis**, teknik enumerasi port menggunakan Nmap, serta cara bernavigasi dan mengambil data (*flag*) di dalam struktur penyimpanan *key-value*.

---

##  Tools & Lingkungan Kerja
* **Target IP:** `[IP_TARGET_REDEEMER]`
* **OS Target:** Linux (Docker container)
* **Tools Utama:**
  * **OpenVPN:** Menghubungkan jaringan lokal ke infrastruktur lab HTB.
  * **Nmap:** Pemindaian port dan deteksi layanan aktif.
  * **redis-cli:** *Command-line interface* untuk berinteraksi dengan server Redis.
  * **Linux Terminal (Kali Linux):** Lingkungan eksekusi perintah.

---

##  Konsep Utama & Teori Dasar
* **Database In-Memory (Redis):** Remote Dictionary Server adalah sistem penyimpanan struktur data *in-memory* open-source yang berfungsi sebagai database, *cache*, dan *message broker*. Karena data disimpan di dalam RAM, akses baca-tulis menjadi sangat cepat.
* **Struktur Key-Value & Multiple Databases:** Redis menggunakan sistem kunci-nilai dan mendukung beberapa database terpisah (secara default menyediakan 16 database dari indeks 0 sampai 15) yang dapat diakses menggunakan perintah `SELECT`.

---

##  Langkah-langkah Penetrasi (Step-by-Step)

### Tahap 1: Koneksi Jaringan & Inisialisasi VPN
* Pastikan koneksi ke jaringan lab Hack The Box aktif menggunakan konfigurasi OpenVPN.
* Lakukan verifikasi jalur komunikasi menggunakan perintah `ping` untuk memastikan target hidup dan dapat dijangkau.

### Tahap 2: Enumerasi Port & Layanan (Nmap)
Lakukan pemindaian port untuk mengetahui layanan apa saja yang berjalan pada target:
```bash
nmap -sV [IP_TARGET_REDEEMER]
```
**Hasil Pemindaian:** Menunjukkan port TCP terbuka untuk layanan Redis pada port default:
* `6379/tcp open redis`

### Tahap 3: Interaksi dengan Layanan Redis
Hubungkan ke server Redis menggunakan utilitas `redis-cli` tanpa autentikasi (karena konfigurasi default tanpa *password*):
```bash
redis-cli -h [IP_TARGET_REDEEMER]
```

### Tahap 4: Eksplorasi Database & Pengambilan Flag
1. Periksa daftar *keys* yang tersedia di dalam database aktif (atau pindah database menggunakan `SELECT <nomor>` jika diperlukan):
   ```text
   keys *
   ```
2. Ambil nilai dari kunci yang menyimpan target *flag*:
   ```text
   get flag
   ```
3. Salin string *flag* yang dikembalikan oleh sistem untuk menyelesaikan tantangan di platform Hack The Box.

> **FLAG:** `[Masukkan string flag yang Anda temukan di sini]`

---

##  Kesimpulan & Rekomendasi Keamanan (Remediation)
1. **Batasi Akses Jaringan (Network Segmentation):** Layanan database seperti Redis tidak boleh terekspos langsung ke publik atau internet tanpa aturan *firewall* yang ketat (seperti membatasi akses hanya via `localhost` atau jaringan internal).
2. **Terapkan Autentikasi (Requirepass):** Konfigurasikan server Redis dengan kata sandi yang kuat menggunakan direktif `requirepass` pada berkas konfigurasi (`redis.conf`) guna mencegah akses dan pembacaan data secara bebas oleh pihak luar.

---

