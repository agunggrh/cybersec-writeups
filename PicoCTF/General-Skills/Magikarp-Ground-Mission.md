# Writeups : PicoCTF - Magikarp Ground Mission

## Ringkasan

Repositori ini berisi panduan dan langkah-langkah lengkap untuk menyelesaikan tantangan dasar SSH dan navigasi direktori Linux di platform PicoCTF.

---

##  Informasi Koneksi Tantangan
- **Tipe Tantangan:** SSH / Dasar Linux / Navigasi File
- **Host Tujuan:** `wily-courier.picoctf.net`
- **Port:** `51832`
- **Username:** `ctf-player`
- **Password:** `8c606eb1`

---

##  Langkah-Langkah Penyelesaian (Walkthrough)

### Langkah 1: Melakukan Koneksi via SSH
Untuk mulai masuk ke dalam server kontainer tantangan, gunakan perintah SSH berikut di terminal lokal kamu:
```bash
ssh ctf-player@wily-courier.picoctf.net -p 51832
```
*Catatan: Saat diminta memasukkan password (`8c606eb1`), karakter tidak akan muncul di layar demi keamanan (itu adalah hal yang normal).*

### Langkah 2: Menjelajahi Direktori Saat Ini
Setelah berhasil login, periksa file dan folder yang ada di direktori kerja kamu saat ini menggunakan perintah:
```bash
ls
```

### Langkah 3: Berpindah ke Direktori Root (`/`)
Pindah ke direktori paling utama (akar dari seluruh sistem file Linux):
```bash
cd /
```
Kamu bisa melihat isi dari direktori sistem utama ini dengan mengetik `ls`.

### Langkah 4: Pulang ke Direktori Home (`~`)
Kembali ke direktori home milik user `ctf-player` (jalur cepat menggunakan simbol tilde):
```bash
cd ~
```
*(Atau kamu juga bisa cukup mengetik `cd` saja tanpa tambahan apa pun).*

### Langkah 5: Membaca File Flag
Di dalam direktori home, terdapat file seperti `3of3.flag.txt` dan folder `drop-in`. Baca isi file tersebut untuk mendapatkan potongan flag:
```bash
cat 1of3.flag.txt
cat 2of3.flag.txt
cat 3of3.flag.txt
```

---

##  Daftar Perintah Dasar Linux yang Dipelajari
- `ssh [user]@[host] -p [port]`: Masuk ke server jarak jauh secara aman.
- `ls`: Menampilkan daftar file dan folder dalam satu direktori.
- `cd [path]`: Berpindah dari satu direktori ke direktori lainnya.
- `pwd`: Menampilkan jalur (path) direktori tempat kamu berada saat ini.
- `cat [nama_file]`: Membaca dan menampilkan isi file teks langsung di terminal.
