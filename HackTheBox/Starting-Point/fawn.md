# Write-up: Fawn (Hack The Box - Starting Point)

## Ringkasan

Dokumen ini merupakan catatan penetrasi (write-up) resmi untuk modul **Fawn** di platform **Hack The Box (HTB)**. Lab pengantar ini dirancang untuk memberikan pemahaman dasar mengenai cara kerja protokol File Transfer Protocol (FTP), penggunaan klien FTP interaktif, eksploitasi fitur login anonim (*anonymous login*), serta teknik pengambilan file sensitif dari server target.

## Tools & Lingkungan Kerja

* **Target IP:** `<IP_Target>` (Mesin Fawn HTB)
* **Tools Utama:**
  * **Nmap** (Port Scanner & Service Version Detection)
  * **FTP Client (CLI)** (Klien baris perintah bawaan Linux untuk berinteraksi dengan server FTP)

## Konsep Utama & Teori Dasar

### 1. File Transfer Protocol (FTP)
Protokol standar jaringan yang digunakan untuk mentransfer file antara klien dan server melalui jaringan komputer. FTP umumnya beroperasi pada port **21 (TCP)** untuk kanal pengendali (*control connection*) dan port **20 (TCP)** untuk kanal data (*data connection*).

### 2. Layanan Anonymous FTP
Fitur pada server FTP yang memungkinkan pengguna luar atau tidak terdaftar untuk mengakses file publik tanpa harus memiliki akun atau kata sandi khusus. Pengguna umumnya menggunakan username `anonymous` dan memasukkan alamat email acak sebagai sandi.

### 3. Kode Tanggapan Server FTP (Response Codes)
* **2xx (Success):** Menandakan bahwa perintah berhasil diterima dan diproses oleh server. Contoh: `230 Login successful.`
* **3xx (Intermediate):** Memerlukan informasi tambahan (seperti kata sandi setelah memasukkan username). Contoh: `331 Please specify the password.`

## Langkah-langkah Penetrasi (Step-by-Step)

### Tahap 1: Pengumpulan Informasi & Pemindaian Port (Nmap)

Pengujian diawali dengan memindai port terbuka dan identifikasi versi layanan pada mesin target menggunakan Nmap:
```bash
nmap -sV <IP_Target>
```

**Hasil Pemindaian:**
* Port 21/tcp terbuka menjalankan layanan FTP dengan versi **`vsftpd 3.0.3`**.
* Jenis sistem operasi target terdeteksi sebagai **`Unix`**.

### Tahap 2: Autentikasi Menggunakan Anonymous Login

Melakukan koneksi ke server FTP target menggunakan klien terminal lokal:
```bash
ftp <IP_Target>
```
* **Username:** `anonymous`
* **Password:** (Dikosongkan atau diisi email bebas, misal: `anonymous@htb.com`)
* **Respon Server:** Menerima kode tanggapan **`230 Login successful.`**, menandakan akses berhasil tanpa akun terdaftar.

### Tahap 3: Eksplorasi Direktori & Pencarian Flag

Setelah berhasil masuk ke sesi interaktif FTP (`ftp>`), jalankan perintah dasar untuk melihat isi direktori:
```text
ftp> ls
```
Ditemukan sebuah file teks mencurigakan di direktori utama, yaitu **`flag.txt`**.

### Tahap 4: Pengunduhan Berkas Target

Mengunduh file `flag.txt` dari server FTP ke komputer lokal menggunakan perintah `get`:
```text
ftp> get flag.txt
```
Setelah proses unduh selesai, akhiri sesi FTP:
```text
ftp> exit
```

### Tahap 5: Membaca Isi Flag

Buka dan tampilkan isi file `flag.txt` di terminal lokal Anda:
```bash
cat flag.txt
```

## Kesimpulan & Rekomendasi Keamanan (Remediation)

* **Nonaktifkan Anonymous FTP:** Jika server FTP tidak ditujukan untuk publik, fitur login anonim wajib dimatikan di file konfigurasi (seperti `anonymous_enable=NO` pada `vsftpd.conf`).
* **Batasi Akses Jaringan (Firewall):** Batasi akses port FTP 21 hanya untuk alamat IP internal atau tepercaya menggunakan firewall (seperti `iptables` atau `ufw`).
* **Gunakan Protokol Terenkripsi:** Pertimbangkan beralih dari FTP standar yang tidak terenkripsi ke protokol yang lebih aman seperti **SFTP** (SSH File Transfer Protocol) atau **FTPS** untuk mencegah penyadapan kredensial di jalur jaringan.
