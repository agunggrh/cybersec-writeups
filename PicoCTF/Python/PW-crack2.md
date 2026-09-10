# Writeups: PicoCTF - PW Crack 2
Repositori ini berisi panduan langkah-demi-langkah untuk menyelesaikan tantangan (Python in CTF's) di PicoCTF berjudul "PW Crack 2", di mana kita diminta untuk menerjemahkan nilai heksadesimal pada kode Python untuk menemukan password tersembunyi.

## Deskripsi Tantangan
* **Tipe Tantangan:** Python in CTF's
* **Instruksi Utama:** Can you crack the password to get the flag?
* **File yang Disediakan:** `level2.py` dan `level2.flag.txt.enc`

## Langkah-Langkah Penyelesaian (Walkthrough)

### Langkah 1: Mengunduh File yang Dibutuhkan
Unduh file skrip Python dan file terenkripsi dari halaman tantangan menggunakan perintah `curl` langsung di terminal kerja kamu:

```bash
curl -O https://artifacts.picoctf.net/c/531/level2.py
curl -O https://artifacts.picoctf.net/c/531/level2.flag.txt.enc
```
*(Catatan: Sesuaikan URL dengan link unduhan resmi yang tertera pada halaman tantangan PicoCTF kamu).*

### Langkah 2: Menganalisis Skrip Menggunakan Nano
Buka file skrip menggunakan teks editor berbasis terminal seperti `nano`:

```bash
nano level2.py
```
*(Petunjuk: Untuk keluar dari nano, tekan `Ctrl + X`)*

### Langkah 3: Menemukan Password & Cara Menerjemahkan Heksadesimal
Perhatikan bagian fungsi pengecekan password di dalam skrip `level2.py`:

```python
def level_2_pw_check():
    user_pw = input("Please enter correct password for flag: ")
    if( user_pw == chr(0x64) + chr(0x65) + chr(0x37) + chr(0x36) ):
        print("Welcome back... your flag, user:")
        decryption = str_xor(flag_enc.decode(), user_pw)
        print(decryption)
        return
    print("That password is incorrect")
```

####  Cara Terjemahan Nilai Heksadesimal (`chr(0x...)`)
Pembuat soal menyembunyikan karakter password menggunakan representasi kode heksadesimal ASCII/Unicode (`chr()`). Kita dapat menerjemahkannya satu per satu menggunakan tabel ASCII atau dengan menjalankan interpreter Python cepat:

1. `0x64` $
ightarrow$ setara dengan desimal `100` $
ightarrow$ karakter huruf **`d`**
2. `0x65` $
ightarrow$ setara dengan desimal `101` $
ightarrow$ karakter huruf **`e`**
3. `0x37` $
ightarrow$ setara dengan desimal `55` $
ightarrow$ karakter angka **`7`**
4. `0x36` $
ightarrow$ setara dengan desimal `54` $
ightarrow$ karakter angka **`6`**

Jika digabungkan (`"d" + "e" + "7" + "6"`), maka password yang benar adalah **`de76`**.

*(Tips Cepat: Kamu bisa langsung menerjemahkannya di terminal Python dengan mengetik `python3` lalu menjalankan `print(chr(0x64) + chr(0x65) + chr(0x37) + chr(0x36))`)*

### Langkah 4: Menjalankan Skrip dan Memasukkan Password
Jalankan program Python tersebut:

```bash
python3 level2.py
```
Masukkan password `de76` saat diminta oleh program.

## Flag
```plaintext
picoCTF{}
```
*(Catatan: Sesuaikan string flag dengan hasil aktual yang keluar di terminal kamu saat berhasil dijalankan).*

---

## Daftar Perintah Linux / Python yang Digunakan
* `curl -O [URL]`: Mengunduh file dari internet ke direktori lokal secara langsung.
* `python3 [nama_file.py]`: Menjalankan program atau skrip berbasis bahasa Python 3.
* `nano [nama_file]`: Membuka teks editor bawaan terminal untuk mengedit atau melihat isi file teks/kode.
