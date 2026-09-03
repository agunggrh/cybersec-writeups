# Writeups: PicoCTF - Static ain't always noise

Repositori ini berisi panduan langkah-demi-langkah untuk menyelesaikan tantangan reverse engineering di PicoCTF yang berjudul **"Static ain't always noise"**, di mana kita diminta untuk memeriksa data di dalam file binary dengan bantuan script bash yang disediakan.

---

##  Deskripsi Tantangan
- **Tipe Tantangan:** Reverse Engineering / Analisis Binary
- **Instruksi Utama:** *Can you look at the data in this binary? The bash script might help!*
- **File yang Disediakan:** `static`, `ltdis.sh`

---

##  Langkah-Langkah Penyelesaian (Walkthrough)

### Langkah 1: Memeriksa dan Menyiapkan File
Pastikan file binary `static` dan script bantuan `ltdis.sh` sudah berada di dalam direktori kerja kamu. Jika diperlukan, berikan izin eksekusi terlebih dahulu pada script bash menggunakan perintah `chmod`:
```bash
chmod +x ltdis.sh
```

### Langkah 2: Menjalankan Script Disassembly
Jalankan script bash tersebut dengan memasukkan file binary `static` sebagai argumennya. Perintah ini akan melakukan disassembly dan mengekstrak string dari binary:
```bash
bash ltdis.sh static
```
Perintah ini akan menghasilkan dua file output utama:
- `static.ltdis.x86_64.txt` (Berisi kode disassembly)
- `static.ltdis.strings.txt` (Berisi kumpulan teks/strings yang terekstrak beserta offset file)

### Langkah 3: Mencari Flag Menggunakan `grep`
Karena flag biasanya tersimpan di dalam teks yang ada di dalam binary, gunakan perintah `grep` untuk menyaring string yang terekstrak pada `static.ltdis.strings.txt` dan temukan format flag (`picoCTF{...}`):
```bash
cat static.ltdis.strings.txt | grep pico
```

## Flag
```picoCTF{d15a5m_t34s3r_20335e41}```

---

##  Daftar Perintah Linux yang Digunakan
- `chmod +x [nama_script]`: Memberikan hak akses eksekusi pada file script.
- `bash [nama_script] [argumen]`: Menjalankan script bash menggunakan interpreter bash.
- `cat [nama_file] | grep [kata_kunci]`: Membaca isi file dan menyaring baris tertentu yang mengandung kata kunci (seperti `pico`).
