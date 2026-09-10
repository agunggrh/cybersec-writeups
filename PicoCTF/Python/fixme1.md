# Writeups: PicoCTF - fixme1.py
Repositori ini berisi panduan langkah-demi-langkah untuk menyelesaikan tantangan pemrograman dasar (Python in CTF's) di PicoCTF yang berjudul "fixme1.py", di mana kita diminta untuk memperbaiki kesalahan syntax dan indentasi pada script Python agar flag-nya dapat ditampilkan.

## Deskripsi Tantangan
* **Tipe Tantangan:** Python in CTF's
* **Instruksi Utama:** Fix the syntax error in this Python script to print the flag.
* **File yang Disediakan:** `fixme1.py`

## Langkah-Langkah Penyelesaian (Walkthrough)

### Langkah 1: Mengunduh File Skrip
Unduh file skrip Python dari halaman tantangan menggunakan perintah `curl` langsung di terminal kerja kamu:

```bash
curl -O https://artifacts.picoctf.net/c/25/fixme1.py
```

### Langkah 2: Menganalisis Error pada Skrip
Ketika mencoba menjalankan file Python tersebut sebelum diperbaiki:

```bash
python3 fixme1.py
```
Terminal akan mengeluarkan pesan error berupa `IndentationError: unexpected indent` pada baris tertentu akibat kelebihan spasi/tab atau masalah format baris.

### Langkah 3: Memperbaiki Kesalahan (Indentasi & Kode)
Buka file menggunakan teks editor berbasis terminal seperti `nano`:

```bash
nano fixme1.py
```
Periksa baris kode terakhir yang melakukan fungsi `print`. Pastikan baris tersebut rata kiri tanpa ada spasi/tab liar di depannya, dan ubah kodenya menjadi seperti ini:

```python
print('That is correct! Here's your flag: ' + flag)
```
Simpan perubahan di dalam editor (`Ctrl + O`, lalu `Enter`) dan keluar (`Ctrl + X`).

### Langkah 4: Menjalankan Ulang Skrip
Setelah indentasi dan sintaksis diperbaiki, jalankan kembali program Python tersebut:

```bash
python3 fixme1.py
```

## Flag
```plaintext
picoCTF{...}
```
*(Catatan: Sesuaikan string flag dengan hasil asli yang keluar di terminal kamu saat berhasil dijalankan).*

---

## Daftar Perintah Linux / Python yang Digunakan
* `curl -O [URL]`: Mengunduh file dari internet ke direktori lokal secara langsung.
* `python3 [nama_file.py]`: Menjalankan program atau skrip berbasis bahasa Python 3.
* `nano [nama_file]`: Membuka teks editor bawaan terminal untuk mengedit isi file teks/kode.
