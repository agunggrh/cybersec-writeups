# Writeups: PicoCTF - PW crack 1
Repositori ini berisi panduan langkah-demi-langkah untuk menyelesaikan tantangan (Python in CTF's) di PicoCTF yang berjudul "PW Crack 1", di mana kita diminta untuk menganalisis skrip Python dan menemukan password tersembunyi agar flag terdekripsi dengan benar.

## Deskripsi Tantangan
* **Tipe Tantangan:** Python in CTF's
* **Instruksi Utama:** Can you crack the password to get the flag?
* **File yang Disediakan:** `level1.py` dan `level1.flag.txt.enc`

## Langkah-Langkah Penyelesaian (Walkthrough)

### Langkah 1: Mengunduh File yang Dibutuhkan
Unduh file skrip Python dan file terenkripsi dari halaman tantangan menggunakan perintah `curl` langsung di terminal kerja kamu:

```bash
curl -O https://artifacts.picoctf.net/c/529/level1.py
curl -O https://artifacts.picoctf.net/c/529/level1.flag.txt.enc
```
*(Catatan: Sesuaikan URL dengan link unduhan resmi yang tertera pada halaman tantangan PicoCTF kamu).*

### Langkah 2: Menganalisis Skrip Menggunakan Nano
Buka file skrip menggunakan teks editor berbasis terminal seperti `nano` (sesuai petunjuk soal):

```bash
nano level1.py
```
*(Petunjuk: Untuk keluar dari nano, tekan `Ctrl + X`)*

### Langkah 3: Menemukan Password pada Kode
Perhatikan fungsi pengecekan password di dalam skrip `level1.py`. Berdasarkan petunjuk soal, fungsi enkripsi `str_xor` tidak perlu dibongkar. Kita cukup mencari nilai pembanding string password yang di-hardcode:

```python
def level_1_pw_check():
    user_pw = input("Please enter correct password for flag: ")
    if( user_pw == "1e1a"):
        print("Welcome back... your flag, user:")
        decryption = str_xor(flag_enc.decode(), user_pw)
        print(decryption)
        return
    print("That password is incorrect")
```
Dari baris `if( user_pw == "1e1a"):`, kita dapat mengetahui bahwa password yang benar adalah **`1e1a`**.

### Langkah 4: Menjalankan Skrip dan Memasukkan Password
Jalankan program Python tersebut:

```bash
python3 level1.py
```
Masukkan password `1e1a` saat diminta oleh program.

## Flag
```plaintext
picoCTF{...}
```
*(Catatan: Sesuaikan string flag dengan hasil aktual yang keluar di terminal kamu saat berhasil dijalankan).*

---

## Daftar Perintah Linux / Python yang Digunakan
* `curl -O [URL]`: Mengunduh file dari internet ke direktori lokal secara langsung.
* `python3 [nama_file.py]`: Menjalankan program atau skrip berbasis bahasa Python 3.
* `nano [nama_file]`: Membuka teks editor bawaan terminal untuk mengedit atau melihat isi file teks/kode.
