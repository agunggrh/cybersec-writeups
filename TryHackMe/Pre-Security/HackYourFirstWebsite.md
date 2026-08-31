# Write-up: Hack Your First Website (TryHackMe)

## Ringkasan
Dokumen ini merupakan catatan penetrasi (*write-up*) resmi untuk modul **Hack Your First Website** di platform [TryHackMe](https://tryhackme.com). Lab ini dirancang untuk memberikan pemahaman dasar mengenai cara kerja komunikasi protokol HTTP, teknik pencarian direktori tersembunyi (*directory enumeration*), analisis struktur header HTTP, serta eksekusi eksploitasi manipulasi parameter dan metode HTTP (`GET` & `POST`).

---

## Tools & Lingkungan Kerja
* **Target Domain:** `http://fakebank.thm`
* **Tools Utama:**
  * `dirb` (Web Content Scanner / Directory Enumeration Tool)
  * Web Browser / Developer Tools (Simulasi Intersepting & Manipulasi Request HTTP)

---

## Konsep Utama & Teori Dasar

### 1. Directory Enumeration
Teknik *brute-forcing* lokasi direktori atau file rahasia yang tidak ditampilkan pada navigasi publik website. Menguji kelemahan *Security Through Obscurity* (keamanan yang hanya mengandalkan persembunyian URL).

### 2. Header HTTP Vital
* **`User-Agent`**: Identitas client (browser, OS) yang dikirimkan ke server. Sering di-spoofing untuk bypass pembatasan akses.
* **`Set-Cookie` & `Cookie`**: Mekanisme server dan browser dalam mengelola state sesi pengguna (session handling).
* **`Content-Type`**: Menentukan format data pada body request. Untuk pengajuan formulir baku menggunakan `application/x-www-form-urlencoded`.

### 3. Perbedaan Metode HTTP (`GET` vs `POST`)
* **`GET`**: Meminta data dari server. Data dikirim melalui parameter URL (terlihat transparan di address bar).
* **`POST`**: Mengirimkan data untuk diproses server. Data sensitif ditempatkan di dalam *body request* secara tersembunyi.

---

## Langkah-langkah Penetrasi (Step-by-Step)

### Tahap 1: Pengumpulan Informasi & Directory Enumeration (`dirb`)
Pengujian diawali dengan memindai struktur direktori tersembunyi pada domain target `http://fakebank.thm`.

```bash
dirb http://fakebank.thm
```

**Hasil Pemindaian:**
* `/images` (Direktori Publik)
* `/bank-transfer` (**Endpoint Tersembunyi Ditemukan**)

---

### Tahap 2: Eksploitasi Logika Bisnis & Manipulasi Saldo
1. Mengakses endpoint sensitif di `http://fakebank.thm/bank-transfer`.
2. Ditemukan fitur transfer/deposit tanpa otentikasi yang memadai.
3. Melakukan eksekusi deposit ke nomor rekening target **`8881`** sebesar **`$2000`**.
4. Kembali ke halaman utama untuk mengonfirmasi saldo rekening. Saldo berhasil dimanipulasi menjadi positif.
5. Pemicu keamanan (*security trigger*) berhasil diaktifkan dan menampilkan bukti penetrasi:

> **FLAG:** `BANK-HACKED`

---

### Tahap 3: Analisis & Manipulasi Parameter GET
Mengubah parameter pada baris request HTTP utama untuk mengakses konten spesifik:

* **Tugas:** Meminta halaman `/blog` dengan ID spesifik `1`.
* **Sintaks Request:**
  ```http
  GET /blog?id=1 HTTP/1.1
  Host: fakebank.thm
  ```

---

### Tahap 4: Penetrasi Formulir Login via POST Request
Mengirimkan data kredensial login menggunakan metode HTTP `POST` langsung ke endpoint `/login`.

* **Identifikasi Masalah (Pembelajaran):**
  Format penulisan data body *form-encoded* harus berupa pasangan `key=value` yang digabungkan dengan ampersand (`&`). Penulisan terbalik seperti `username=password&thm=letmein` akan ditolak oleh parser server.

* **Eksekusi Request POST yang Benar:**
  ```http
  POST /login HTTP/1.1
  Host: fakebank.thm
  User-Agent: Mozilla/5.0 (X11; Linux x86_64)
  Content-Type: application/x-www-form-urlencoded
  Content-Length: 29

  username=thm&password=letmein
  ```

**Respon Server:**
Server mengembalikan status `200 OK`, mengautentikasi kredensial, dan mengembalikan respon sukses penuntasan modul.

---

## Kesimpulan & Rekomendasi Keamanan (Remediation)

1. **Hindari Security Through Obscurity:**
   Direktori sensitif seperti `/bank-transfer` tidak boleh hanya disembunyikan dari tautan navigasi, melainkan wajib dilindungi dengan autentikasi & otorisasi (*Role-Based Access Control*).
2. **Validasi Sisi Server (Server-Side Validation):**
   Setiap parameter transaksi finansial atau deposit wajib divalidasi dan diverifikasi di sisi server untuk mencegah manipulasi saldo ilegal dari sisi client.
3. **Penerapan Header Keamanan:**
   Pastikan penanganan sesi cookie menggunakan atribut `HttpOnly` dan `Secure` untuk mencegah pencurian token sesi.
