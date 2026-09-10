# Writeups: PicoCTF - fixme2.py
Repositori ini berisi panduan langkah-demi-langkah untuk menyelesaikan tantangan pemrograman dasar (Python in CTF's) di PicoCTF yang berjudul "fixme2.py", di mana kita diminta untuk memperbaiki kesalahan penggunaan operator perbandingan pada skrip Python agar flag-nya dapat ditampilkan.

## Deskripsi Tantangan
* **Tipe Tantangan:** Python in CTF's
* **Instruksi Utama:** Fix the syntax error in this Python script to print the flag.
* **File yang Disediakan:** `fixme2.py`

## Langkah-Langkah Penyelesaian (Walkthrough)

### Langkah 1: Mengunduh File Skrip
Unduh file skrip Python dari halaman tantangan menggunakan perintah `curl` langsung di terminal kerja kamu:

```bash
curl -O https://artifacts.picoctf.net/c/6/fixme2.py
```
*(Catatan: Sesuaikan URL dengan link unduhan resmi yang tertera pada halaman tantangan PicoCTF kamu).*

### Langkah 2: Menganalisis Error pada Skrip
Ketika mencoba menjalankan file Python tersebut sebelum diperbaiki:

```bash
python3 fixme2.py
```
Terminal akan mengeluarkan pesan error berupa:
```plaintext
  File "fixme2.py", line 22
    if flag = "":
       ^^^^^^^^^
SyntaxError: invalid syntax. Maybe you meant '==' or ':=' instead of '='?
```
Pesan error ini mengindikasikan adanya penggunaan operator penugasan (`=`) yang keliru di dalam struktur kondisi `if` (seharusnya menggunakan operator perbandingan kesamaan `==`).

### Langkah 3: Memperbaiki Kesalahan (Operator Perbandingan)
Buka file menggunakan teks editor berbasis terminal seperti `nano`:

```bash
nano fixme2.py
```
Cari bagian percabangan `if` yang memeriksa kondisi string kosong, lalu ubah tanda `=` tunggal menjadi dua sama dengan (`==`):

**Sebelum (Error):**
```python
if flag = "":
    print('String XOR encountered a problem, quitting. ')
```

**Sesudah (Benar):**
```python
if flag == "":
    print('String XOR encountered a problem, quitting. ')
```
Simpan perubahan di dalam editor (`Ctrl + O`, lalu `Enter`) dan keluar (`Ctrl + X`).

### Langkah 4: Menjalankan Ulang Skrip
Setelah sintaksis dan operator perbandingannya diperbaiki, jalankan kembali program Python tersebut:

```bash
python3 fixme2.py
```

## Flag
```plaintext
picoCTF{...}
```
*(Catatan: Sesuaikan string flag dengan hasil aktual yang keluar di terminal kamu saat berhasil dijalankan).*

---

## Daftar Perintah Linux / Python yang Digunakan
* `curl -O [URL]`: Mengunduh file dari internet ke direktori lokal secara langsung.
* `python3 [nama_file.py]`: Menjalankan program atau skrip berbasis bahasa Python 3.
* `nano [nama_file]`: Membuka teks editor bawaan terminal untuk mengedit isi file teks/kode.
