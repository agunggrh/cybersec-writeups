# Writeups: PicoCTF - Ekstraksi Arsip & Navigasi File Tersembunyi

Repositori ini berisi panduan langkah-demi-langkah untuk menyelesaikan tantangan CTF yang melibatkan ekstraksi file arsip (ZIP) serta pencarian file target (`uber-secret.txt`) yang berada di dalam direktori berlapis dan folder tersembunyi di Linux.

---

##  Deskripsi Tantangan
- **Tipe Tantangan:** Ekstrak Arsip / Navigasi File / Direktori Tersembunyi
- **Instruksi Utama:** *Unzip this archive and find the file named 'uber-secret.txt'*

---

## Langkah-Langkah Penyelesaian (Walkthrough)

### Langkah 1: Mengekstrak File Arsip (ZIP)
Sebelum mencari file, pastikan file arsip tantangan sudah diekstrak ke direktori kerja kamu. Gunakan perintah `unzip`:
```bash
unzip nama_file_arsip.zip
```
*(Perintah ini akan mengekstrak seluruh folder dan file bawaan dari arsip tersebut).*

### Langkah 2: Mencari Lokasi File Menggunakan `find`
Karena struktur folder di dalam arsip cukup dalam dan rumit, jangan menebak-nebak jalurnya. Gunakan perintah pencarian `find` untuk melacak file `uber-secret.txt`:
```bash
find . -name "uber-secret.txt"
```
Perintah ini akan memindai direktori saat ini secara menyeluruh dan menghasilkan jalur (path) yang akurat, misalnya:
```text
./files/adequate_books/more_books/.secret/deeper_secrets/deepest_secrets/uber-secret.txt
```

### Langkah 3: Membaca Isi File Rahasia
Setelah jalur file ditemukan (termasuk melewati folder tersembunyi yang diawali dengan titik seperti `.secret`), gunakan perintah `cat` untuk membaca isinya dan mendapatkan flag:
```bash
cat ./files/adequate_books/more_books/.secret/deeper_secrets/deepest_secrets/uber-secret.txt
```

---

##  Daftar Perintah Linux yang Digunakan
- `unzip [nama_file.zip]`: Mengekstrak file arsip terkompresi berformat ZIP.
- `find . -name "[nama_file]"`: Mencari file secara spesifik berdasarkan nama di seluruh sub-folder secara rekursif.
- `cat [jalur_file]`: Membaca dan menampilkan isi file teks langsung di terminal.
