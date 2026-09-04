# Write-up: Meow (Hack The Box - Starting Point)

## Ringkasan

Dokumen ini merupakan catatan penetrasi (*write-up*) resmi untuk lab **Meow** di platform **Hack The Box (Starting Point)**. Lab ini dirancang untuk memperkenalkan konsep dasar pengujian penetrasi jaringan, interaksi dengan layanan remote menggunakan protokol jaringan standar, serta teknik dasar autentikasi pada layanan Telnet.

## Tools & Lingkungan Kerja

* **Target IP**: `[IP_TARGET_MEOW]`
* **OS Target**: Linux (Minimal/Docker container)
* **Tools Utama**:
  * **OpenVPN**: Untuk menghubungkan jaringan lokal ke infrastruktur lab Hack The Box.
  * **Telnet**: Protokol jaringan dan klien CLI untuk berinteraksi langsung dengan port layanan target (Port 23).
  * **Linux Terminal (Kali Linux)**: Sebagai lingkungan eksekusi perintah.

## Konsep Utama & Teori Dasar

1. **Protokol Telnet (Port 23)**
   * Protokol jaringan lama yang digunakan untuk komunikasi teks dua arah secara interaktif menggunakan koneksi virtual *plaintext*. Karena sifatnya yang tidak terenkripsi, Telnet jarang digunakan pada sistem produksi modern namun sering dijumpai pada perangkat lama atau *embedded systems*.
2. **Manajemen Akses Root (Privilege / Superuser)**
   * Akun `root` adalah akun administrator tertinggi pada sistem operasi berbasis Unix/Linux yang memiliki kontrol penuh terhadap seluruh berkas dan konfigurasi sistem.
3. **Pencarian Berkas Sistem (File Searching)**
   * Teknik navigasi direktori dan pencarian berkas spesifik (seperti `flag.txt`) menggunakan utilitas bawaan Linux seperti perintah `pwd`, `ls`, dan `find`.

## Langkah-langkah Penetrasi (Step-by-Step)

### Tahap 1: Koneksi Jaringan & Inisialisasi VPN

* Memastikan koneksi ke jaringan lab Hack The Box aktif menggunakan konfigurasi OpenVPN dengan protokol TCP/UDP.
* Melakukan verifikasi jalur komunikasi menggunakan perintah *ping* untuk memastikan target hidup dan dapat dijangkau dari mesin penyerang.

### Tahap 2: Enumerasi Port & Layanan (Telnet)

* Melakukan koneksi interaktif ke mesin target menggunakan protokol Telnet pada port standar `23`:
  ```bash
  telnet [IP_TARGET_MEOW] 23

* Respon server mengembalikan prompt autentikasi berupa permintaan login: Meow login:

### Tahap 3: Autentikasi & Akses Sistem (Root Login)

* Memasukkan default credential yang sering digunakan pada modul pemula:
  * Username: root
  * Password: (Dikosongkan / Langsung tekan Enter)

* Server memproses kredensial dan memberikan akses masuk penuh ke dalam sistem target sebagai pengguna root.

### Tahap 4: Eksfiltrasi Data & Pengambilan Flag

* Memeriksa direktori aktif saat ini untuk mencari lokasi berkas flag:
```Bash
pwd
ls -la
```

* Jika berkas tidak ditemukan di direktori saat ini, melakukan pencarian global pada sistem menggunakan perintah find:

```Bash
find / -name "flag.txt" 2>/dev/null
```

* Membaca isi berkas flag yang ditemukan untuk menyelesaikan tantangan:

```Bash
cat /root/flag.txt
```
* FLAG: (Masukkan string flag yang Anda temukan di sini)


### Kesimpulan & Rekomendasi Keamanan (Remediation)

* Hindari Penggunaan Telnet: Protokol Telnet tidak mengenkripsi data yang dikirimkan (termasuk username dan password), sehingga sangat rentan terhadap serangan sniffing di jaringan. Disarankan untuk beralih menggunakan SSH (Secure Shell) yang menyediakan enkripsi end-to-end.
* Penguatan Kata Sandi (Strong Passwords): Layanan sistem tidak boleh dibiarkan tanpa kata sandi (blank password) atau menggunakan kredensial default yang mudah ditebak oleh pihak luar.
