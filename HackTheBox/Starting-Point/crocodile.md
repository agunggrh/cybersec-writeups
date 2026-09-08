# Write-up: Crocodile (Hack The Box - Starting Point)

## Ringkasan
Dokumen ini merupakan catatan penetrasi (write-up) resmi untuk modul **Crocodile** di platform Hack The Box (HTB). Lab ini dirancang untuk memberikan pemahaman dasar mengenai pengenalan port dan versi layanan menggunakan Nmap, eksplorasi layanan FTP dengan *anonymous login*, teknik direktori *brute-forcing* menggunakan Gobuster untuk menemukan file tersembunyi berformat PHP, serta proses autentikasi web untuk mendapatkan flag.

## Tools & Lingkungan Kerja
- **Target IP:** `<IP_Target>` (Mesin Crocodile HTB)
- **Tools Utama:** 
  - **Nmap** (Port Scanner & Service Version Detection via `-sV`)
  - **FTP Client (CLI)** (Klien baris perintah untuk akses layanan FTP anonim)
  - **Gobuster** (Directory/File brute-forcing dengan flag ekstensi `-x`)

## Konsep Utama & Teori Dasar
1. **Service Version Detection (`-sV`)**: Digunakan dalam Nmap untuk mendeteksi aplikasi dan nomor versi yang berjalan pada port terbuka, membantu mengidentifikasi potensi kerentanan spesifik.
2. **Anonymous FTP Authentication**: Fitur konfigurasi server FTP yang mengizinkan akses masuk tanpa kredensial resmi menggunakan username `anonymous`.
3. **Directory Brute-Forcing (`gobuster dir`)**: Teknik menebak nama direktori atau file tersembunyi pada web server menggunakan wordlist kamus. Penggunaan flag `-x php` memungkinkan pencarian spesifik file berformat PHP seperti `login.php`.

## Langkah-langkah Penetrasi (Step-by-Step)

### Tahap 1: Pengumpulan Informasi & Pemindaian Port (Nmap)
Pengujian diawali dengan memindai port terbuka serta mendeteksi versi layanan pada mesin target:
```bash
nmap -sV <IP_Target>
```
**Hasil Pemindaian:**
- Port `21/tcp` terbuka menjalankan layanan FTP (`vsftpd`).
- Port `80/tcp` terbuka menjalankan layanan Web Server HTTP (`Apache httpd`).

### Tahap 2: Akses Layanan FTP & Pengambilan File Kredensial
Melakukan koneksi ke server FTP target menggunakan akun anonim:
```bash
ftp <IP_Target>
```
- **Username:** `anonymous`
- **Password:** *(Kosongkan / tekan Enter)*

Setelah berhasil masuk, tampilkan daftar file di server:
```text
ftp> ls
```
Ditemukan file teks penting seperti `allowed.userlist` dan file terkait lainnya. Unduh file tersebut ke komputer lokal:
```text
ftp> get allowed.userlist
ftp> exit
```
Baca isi file untuk melihat daftar pengguna berhak (ditemukan user utama: `admin`).

### Tahap 3: Pencarian Halaman Web Tersembunyi (Gobuster)
Melakukan pemindaian direktori dan file pada web server untuk mencari halaman autentikasi:
```bash
gobuster dir -u http://<IP_Target> -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-small.txt -x php
```
- Flag `-x php` digunakan agar Gobuster mencari file dengan ekstensi `.php`.
- **Hasil Temuan:** Ditemukan file web penting yaitu `/login.php`.

### Tahap 4: Autentikasi dan Pengambilan Flag
1. Buka browser dan akses URL: `http://<IP_Target>/login.php`
2. Masukkan kredensial yang ditemukan (`admin` beserta kombinasi password yang tersedia).
3. Setelah berhasil login ke dashboard administrasi, temukan dan salin string flag berformat `HTB{...}` yang tertera di halaman tersebut.

## Kesimpulan & Rekomendasi Keamanan (Remediation)
- **Batasi Akses FTP Publik:** Pastikan layanan FTP tidak dikonfigurasi secara longgar yang mengizinkan *anonymous login* jika tidak diperlukan.
- **Sembunyikan Informasi Versi:** Melakukan *hardening* pada banner layanan web dan FTP agar versi aplikasi tidak mudah dibaca secara publik oleh Nmap.
- **Amankan Halaman Admin:** Proteksi halaman login sensitif seperti `login.php` dengan autentikasi multi-faktor atau pembatasan alamat IP (whitelist).
