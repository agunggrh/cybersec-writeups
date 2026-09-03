# Writeups : picoCTF - Python Wrangling

## Ringkasan

Dokumen ini merupakan catatan (write-up) untuk tantangan Python Wrangling di platform picoCTF. Tantangan ini dirancang untuk menguji pemahaman dasar mengenai cara menjalankan skrip Python melalui terminal, memberikan argumen parameter tambahan (seperti `-d` untuk dekripsi), serta memanfaatkan file teks berisi password dan file terenkripsi untuk mendapatkan flag.

---

## Tools & Lingkungan Kerja

Platform Target: picoCTF
Bahasa Pemrograman: Python 3
Tools Utama:
- Terminal / Command Prompt (untuk mengeksekusi skrip Python)
- Teks editor / File explorer (untuk melihat isi file pendukung)

---

## Konsep Utama & Teori Dasar

Eksekusi Skrip Berbasis Argumen
Menjalankan program Python di terminal dengan menyertakan opsi flag tertentu, seperti `-d` untuk melakukan dekripsi data.
Manajemen Berkas Pendukung (Files Management)
Memahami peran file-file yang disediakan dalam tantangan, meliputi skrip utama (`ende.py`), file teks sandi (`password.txt`), dan file target yang terenkripsi (`flag.txt.en`).
Autentikasi Kunci / Password
Memasukkan string kata sandi yang tersimpan di dalam file `password.txt` ke dalam program interaktif untuk mendekripsi isi pesan rahasia.

---

## Langkah-langkah Penyelesaian (Step-by-Step)

### Tahap 1: Memeriksa Berkas yang Disediakan

Pastikan seluruh berkas tantangan berada di dalam direktori kerja yang sama. Berkas-berkas tersebut meliputi:
- `ende.py` (Skrip Python utama)
- `password.txt` (Berkas yang berisi string password/kunci)
- `flag.txt.en` (Berkas data target yang terenkripsi)

### Tahap 2: Menjalankan Skrip untuk Proses Dekripsi

Jalankan skrip `ende.py` menggunakan interpreter Python 3 dengan menyertakan argumen `-d` (decrypt) diikuti oleh nama file terenkripsi `flag.txt.en`:
```bash
python3 ende.py -d flag.txt.en
```

### Tahap 3: Memasukkan Password dari password.txt

Ketika program meminta input kata sandi secara interaktif:
`Please enter the password:`

Buka isi file password.txt (atau gunakan perintah terminal seperti cat password.txt), lalu salin dan masukkan password yang tertera di dalamnya.

### Tahap 4: Pengambilan Flag

Setelah password divalidasi oleh program, teks asli dari file `flag.txt.en` akan didekripsi dan flag picoCTF akan langsung ditampilkan di layar terminal.

## Kesimpulan & Pembelajaran

- Penggunaan Baris Perintah (Command Line): Mempelajari cara mengeksekusi skrip Python secara langsung melalui terminal dengan parameter argumen file input.
- Pengelolaan Data Terenkripsi: Memahami bagaimana skrip dekripsi bekerja secara lokal dengan menggabungkan skrip, file terenkripsi, dan kunci sandi eksternal untuk membongkar informasi rahasia.
