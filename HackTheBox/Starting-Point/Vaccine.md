# Write-up: Vaccine (Hack The Box - Starting Point)

> **Panduan Edukatif & Penetrasi Komprehensif**  
> **Platform:** Hack The Box (Starting Point)  
> **Modul:** Vaccine  
> **Target IP:** `<IP_Target>` (Ganti dengan IP mesin target Anda)  

---

##  Daftar Isi
1. [Ringkasan Eksekutif](#ringkasan-eksekutif)
2. [Tools & Lingkungan Kerja](#tools--lingkungan-kerja)
3. [Konsep Utama & Teori Dasar](#konsep-utama--teori-dasar)
4. [Langkah-Langkah Penetrasi (Step-by-Step)](#langkah-langkah-penetrasi-step-by-step)
   - [Tahap 1: Pengumpulan Informasi & Pemindaian Port (Nmap)](#tahap-1-pengumpulan-informasi--pemindaian-port-nmap)
   - [Tahap 2: Enumerasi Aplikasi Web & Penemuan Celah SQLi](#tahap-2-enumerasi-aplikasi-web--penemuan-celah-sqli)
   - [Tahap 3: Eksploitasi SQLi untuk Membaca Flag User (user.txt)](#tahap-3-esploitasi-sqli-untuk-membaca-flag-user-usertxt)
   - [Tahap 4: Pembocoran Kredensial Database Melalui Source Code](#tahap-4-pembocoran-kredensial-database-melalui-source-code)
   - [Tahap 5: Remote Login via SSH](#tahap-5-remote-login-via-ssh)
   - [Tahap 6: Eskalasi Hak Akses ke Root (Privilege Escalation)](#tahap-6-eskalasi-hak-akses-ke-root-privilege-escalation)
   - [Tahap 7: Pengambilan Flag Root (root.txt)](#tahap-7-pengambilan-flag-root-roottxt)
5. [Kesimpulan & Rekomendasi Keamanan (Remediation)](#kesimpulan--rekomendasi-keamanan-remediation)

---

##  Ringkasan Eksekutif
Dokumen ini merupakan catatan penetrasi (*write-up*) yang sangat mendetail dan komprehensif untuk modul **Vaccine** di platform Hack The Box (HTB). Panduan ini dirancang secara edukatif untuk membantu pembelajar memahami alur eksploitasi menyeluruh—mulai dari pemindaian awal, penemuan celah *Error-Based SQL Injection* pada aplikasi web, pembacaan file sistem sensitif dan *source code*, akses jarak jauh via SSH, hingga teknik eskalasi hak akses (*privilege escalation*) tingkat lanjut menggunakan penyalahgunaan konfigurasi `sudo` dan editor teks `vi`.

---

##  Tools & Lingkungan Kerja
* **Target IP:** `<IP_Target>` (Mesin Vaccine HTB)
* **Tools Utama:**
  * **Nmap:** Port Scanner & Service Enumeration (Mendeteksi port & versi layanan terbuka).
  * **Curl:** Command-Line HTTP Client (Digunakan untuk mengirimkan payload injeksi secara presisi).
  * **SSH (Secure Shell Client):** Akses terminal jarak jauh ke sistem target.
  * **Vim / Vi:** Text editor lokal pada target & vektor *privilege escalation*.

---

##  Konsep Utama & Teori Dasar

### 1. Error-Based SQL Injection (SQLi)
Kerentanan ini terjadi ketika aplikasi web memasukkan input pengguna langsung ke dalam kueri database tanpa sanitasi atau *parameterized queries* yang memadai, lalu menampilkan pesan *error* mentah dari database ke antarmuka pengguna. Penyerang dapat memaksa database untuk memasukkan data sensitif (seperti isi file sistem) ke dalam pesan *error* tersebut menggunakan fungsi konversi tipe data (*type casting*).

### 2. Fungsi Internal PostgreSQL (`pg_read_file()`)
PostgreSQL menyediakan fungsi bawaan bagi pengguna istimewa untuk membaca file sistem operasi lokal secara langsung. Jika kueri SQL dapat dimanipulasi melalui celah injeksi, fungsi ini dapat dieksploitasi untuk mencuri file apa pun yang hak aksesnya dapat dijangkau oleh pengguna sistem basis data (`postgres`).

### 3. Sudo GTFOBins (`/bin/vi`) Privilege Escalation
Konfigurasi `sudoers` yang salah atau terlalu longgar (misalnya mengizinkan user biasa menjalankan editor teks seperti `vi` sebagai `root`) dapat dieksploitasi. Melalui fitur bawaan editor teks yang mengizinkan eksekusi perintah sistem eksternal (*shell escape*), penyerang dapat langsung melompat dari user biasa menjadi `root`.

---

##  Langkah-Langkah Penetrasi (Step-by-Step)

### Tahap 1: Pengumpulan Informasi & Pemindaian Port (Nmap)
Langkah awal dalam setiap pengujian keamanan adalah mengenali layanan apa saja yang aktif pada mesin target menggunakan pemindai port Nmap.

```bash
nmap -sV -sC <IP_Target>
```

#### Analisis Hasil Pemindaian:
* **Port 22/tcp (Open):** Menjalankan layanan SSH (`OpenSSH`).
* **Port 80/tcp (Open):** Menjalankan layanan Web (`Apache HTTPd`).

---

### Tahap 2: Enumerasi Aplikasi Web & Penemuan Celah SQLi
1. Akses alamat web target melalui browser atau menggunakan *command-line*:
   ```text
   http://<IP_Target>
   ```
2. Dari penelusuran manual, ditemukan sebuah situs web katalog mobil yang memiliki fitur pencarian (*search box*) di halaman `dashboard.php`.
3. Fitur pencarian ini menerima input teks, lalu mengirimkan parameter `search` ke dalam kueri database PostgreSQL di backend tanpa validasi atau pembersihan karakter khusus yang aman.
4. Kita dapat menguji kerentanan *Error-Based SQL Injection* dengan menyengaja memicu kesalahan konversi tipe data menggunakan fungsi `CAST(... AS NUMERIC)`. Jika kueri gagal karena tipe data tidak sesuai (misalnya mencoba mengubah string teks murni menjadi angka numerik), database akan membatalkan eksekusi dan mencetak isi dari kueri di dalam pesan *error* web.

---

### Tahap 3: Eksploitasi SQLi untuk Membaca Flag User (`user.txt`)
Karena aplikasi menampilkan pesan *error* database ke antarmuka web, kita dapat menyisipkan fungsi `pg_read_file()` di dalam pernyataan `CAST` untuk membaca file `user.txt`.

Jalankan perintah `curl` berikut untuk mengeksekusi payload:

```bash
curl -s -G "http://<IP_Target>/dashboard.php" \
  --data-urlencode "search=asa' AND 1=CAST((SELECT pg_read_file('/var/lib/postgresql/user.txt', 0, 1000)) AS NUMERIC)--" \
  --cookie "PHPSESSID=<Cookie_Session>"
```

#### Penjelasan Rinci Payload:
* `'`: Menutup string kueri asli agar struktur SQL dapat diubah.
* `AND 1=CAST(...)`: Memaksa konversi hasil fungsi SQL menjadi angka numerik (`NUMERIC`). Karena isi file berupa teks/string, PostgreSQL akan gagal melakukan konversi dan memicu pesan *error*.
* `pg_read_file('/var/lib/postgresql/user.txt', 0, 1000)`: Fungsi PostgreSQL untuk membaca file dari path absolut yang ditentukan (membaca dari byte ke-0 hingga 1000).
* `--`: Karakter komentar dalam SQL untuk mengabaikan sisa kueri asli di belakangnya agar tidak terjadi syntax error tambahan.

> **Hasil:** Pesan *error* pada halaman web mengembalikan isi string flag `user.txt` secara utuh di dalam teks HTML/respons server.

---

### Tahap 4: Pembocoran Kredensial Database Melalui Source Code
Untuk memperluas akses ke sistem (karena akses web terbatas), kita perlu mencari kredensial *login* sistem operasi (seperti SSH). Kita dapat membaca kode sumber file aplikasi web (`dashboard.php`) menggunakan metode pembacaan file yang sama untuk mencari konfigurasi koneksi basis data atau kredensial tersembunyi.

Jalankan perintah `curl` berikut:

```bash
curl -s -G "http://<IP_Target>/dashboard.php" \
  --data-urlencode "search=asa' AND 1=CAST((SELECT pg_read_file('/var/www/html/dashboard.php', 1000, 1000)) AS NUMERIC)--" \
  --cookie "PHPSESSID=<Cookie_Session>"
```
*(Catatan: Anda mungkin perlu mengubah offset parameter `1000, 1000` atau `0, 2000` jika ukuran file cukup besar agar seluruh bagian baris kode kredensial terlihat).*

> **Hasil:** Pesan *error* mengekspos baris kode koneksi PHP yang berisi nama pengguna (`postgres` / user sistem tertentu) beserta kata sandi basis data atau kredensial sistem yang aktif.

---

### Tahap 5: Remote Login via SSH
Dengan mengantongi kredensial sistem yang ditemukan pada tahap sebelumnya, kita melakukan koneksi jarak jauh ke mesin target melalui protokol SSH:

```bash
ssh <Username>@<IP_Target>
```
*(Masukkan kata sandi yang valid saat diminta).*

> **Hasil:** Berhasil masuk ke dalam sistem operasi Linux target dan mendapatkan *shell* interaktif sebagai pengguna biasa terkait. Catat atau simpan sesi ini untuk langkah eskalasi selanjutnya.

---

### Tahap 6: Eskalasi Hak Akses ke Root (Privilege Escalation)
Setelah berada di dalam sistem sebagai pengguna biasa, langkah selanjutnya adalah memeriksa hak istimewa *sudo* yang dimiliki oleh pengguna saat ini untuk melihat apakah ada celah konfigurasi yang dapat disalahgunakan.

Jalankan perintah berikut di terminal SSH:

```bash
sudo -l
```

#### Hasil Pemeriksaan:
```text
User <Username> may run the following commands on vaccine:
    (ALL) /bin/vi /etc/postgresql/11/main/pg_hba.conf
```
*Analisis:* Sistem mengizinkan pengguna untuk mengeksekusi editor teks `/bin/vi` dengan hak akses penuh (`ALL` / root) khusus untuk mengedit file konfigurasi PostgreSQL tertentu.

#### Langkah Eksploitasi Vi / Vim Shell Escape:
1. Jalankan perintah `sudo` yang diizinkan tersebut:
   ```bash
   sudo /bin/vi /etc/postgresql/11/main/pg_hba.conf
   ```
2. Di dalam antarmuka editor `vi`, pastikan Anda keluar dari mode penyisipan/pengetikan teks dengan menekan tombol `Esc` pada keyboard.
3. Ketik perintah *shell escape* untuk menginstruksikan `vi` menjalankan program eksternal di bawah konteks hak akses yang sedang berjalan (root):
   ```text
   :!/bin/bash
   ```
4. Tekan `Enter`. 

> **Hasil:** Prompt terminal akan langsung berubah menjadi simbol hak akses tertinggi (`root@vaccine:/#`), menandakan Anda telah berhasil menjadi *root*.

---

### Tahap 7: Pengambilan Flag Root (`root.txt`)
Setelah berhasil masuk ke dalam *root shell*, jalankan perintah untuk mencari dan membaca file flag utama sistem:

```bash
cat /root/root.txt
```

> **Hasil:** Flag `root.txt` tercetak di layar terminal, menandakan seluruh rangkaian peretasan dan pembelajaran modul ini telah selesai dengan sukses.

---

## 🛡️ Kesimpulan & Rekomendasi Keamanan (Remediation)

1. **Implementasikan Prepared Statements (Parameterized Queries):**  
   Seluruh parameter input dari pengguna (seperti kolom pencarian `search`) wajib dipisahkan dari struktur kueri SQL menggunakan *parameterized queries* atau ORM yang aman guna menutup total celah *SQL Injection*.
2. **Batasi Hak Akses Fungsi Sensitif Database:**  
   Batasi hak istimewa pengguna basis data agar tidak memiliki akses ke fungsi tingkat sistem operasi yang berbahaya seperti `pg_read_file()`, `lo_export()`, atau eksekusi perintah eksternal.
3. **Perketat Konfigurasi Sudoers (`/etc/sudoers`):**  
   Hindari memberikan izin `sudo` pada aplikasi utilitas atau penyunting teks interaktif (seperti `vi`, `nano`, `vim`, `less`, atau program berbasis teks lainnya) karena utilitas tersebut memiliki fitur *shell escape* yang sangat rentan disalahgunakan untuk eskalasi hak akses (*root escalation*).
