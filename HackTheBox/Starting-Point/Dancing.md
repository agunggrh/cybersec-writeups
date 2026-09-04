# Write-up: Dancing (Hack The Box - Starting Point)

## Ringkasan

Dokumen ini merupakan catatan penetrasi (write-up) resmi untuk modul Dancing di platform Hack The Box (HTB). Lab pengantar ini dirancang untuk memberikan pemahaman dasar mengenai cara kerja protokol Server Message Block (SMB), penggunaan klien SMB interaktif, eksplorasi share folder, serta teknik pengambilan file sensitif dari server target menggunakan sesi tanpa kata sandi (null session).

## Tools & Lingkungan Kerja

- **Target IP:** `<IP_Target>` (Mesin Dancing HTB)
- **Tools Utama:**
  - **Nmap** (Port Scanner & Service Version Detection)
  - **smbclient** (Klien baris perintah untuk berinteraksi dengan layanan SMB/CIFS)

## Konsep Utama & Teori Dasar

### 1. Server Message Block (SMB)
Protokol berbagi file jaringan (network file sharing protocol) yang digunakan dalam sistem operasi Microsoft Windows untuk memungkinkan aplikasi di komputer membaca dan menulis ke file serta meminta layanan dari server di jaringan komputer. SMB umumnya beroperasi pada port **445/tcp** (menggunakan Microsoft-DS) atau port 139/tcp (NetBIOS).

### 2. Null Session & Guest Access
Fitur pada server SMB yang mengizinkan pengguna untuk terhubung ke share folder tanpa memerlukan kredensial valid (menggunakan kata sandi kosong atau autentikasi anonim). Jika konfigurasi server tidak aman, hal ini dapat dimanfaatkan untuk mengeksplorasi direktori internal dan mencuri data sensitif.

### 3. Utilitas smbclient
Alat bantu berbasis baris perintah yang berfungsi sebagai klien SMB. Alat ini memungkinkan pengguna untuk melakukan operasi seperti mencantumkan share yang tersedia, bernavigasi antar direktori, mengunggah, dan mengunduh file secara interaktif mirip seperti FTP client.

## Langkah-langkah Penetrasi (Step-by-Step)

### Tahap 1: Pengumpulan Informasi & Pemindaian Port (Nmap)

Pengujian diawali dengan memindai port terbuka dan identifikasi layanan pada mesin target menggunakan Nmap:
```bash
nmap -sV -sC <IP_Target>
```

**Hasil Pemindaian:**
- Port **445/tcp** terbuka menjalankan layanan `microsoft-ds` (SMB).

### Tahap 2: Enumerasi SMB Shares

Melakukan pengecekan daftar share yang aktif di server target menggunakan klien `smbclient`:
```bash
smbclient -L //<IP_Target> -N
```
*(Catatan: `-N` digunakan untuk menonaktifkan permintaan password / autentikasi kosong).*

**Hasil Pemindaian Share:**
- `ADMIN$` (Remote Admin)
- `C$` (Default share)
- `IPC$` (Remote IPC)
- `WorkShares` (Disk share khusus yang dapat diakses publik)

### Tahap 3: Menghubungkan ke Share Target

Melakukan koneksi langsung ke dalam direktori `WorkShares` menggunakan autentikasi kosong:
```bash
smbclient //<IP_Target>/WorkShares -N
```

Setelah berhasil masuk ke prompt interaktif `smb: \>`, jalankan perintah `ls` untuk melihat isi direktori:
```text
smb: \> ls
```
Ditemukan dua folder utama di dalam share tersebut, yaitu direktori `Amy.J` dan `James.P`.

### Tahap 4: Eksplorasi Direktori & Pencarian Flag

Masuk ke dalam folder `James.P` untuk mencari file target:
```text
smb: \> cd James.P
smb: \> ls
```
Di dalam folder tersebut, ditemukan sebuah file teks penting yaitu `flag.txt`.

### Tahap 5: Pengunduhan Berkas Target

Mengunduh file `flag.txt` dari server SMB ke komputer lokal menggunakan perintah `get`:
```text
smb: \> get flag.txt
```

Setelah proses unduh selesai, akhiri sesi SMB:
```text
smb: \> exit
```

### Tahap 6: Membaca Isi Flag

Buka dan tampilkan isi file `flag.txt` di terminal lokal Anda:
```bash
cat flag.txt
```

## Kesimpulan & Rekomendasi Keamanan (Remediation)

1. **Batasi Akses Tanpa Kredensial (Null Session):** Nonaktifkan izin akses baca/tulis anonim atau tamu (*guest/null session*) pada share folder yang sensitif untuk mencegah pencurian informasi.
2. **Terapkan Kontrol Akses Berbasis Peran (RBAC):** Pastikan direktori atau *share* personal (seperti folder pengguna) hanya dapat diakses oleh akun pemilik yang sah menggunakan kredensial terenkripsi yang kuat.
3. **Batasi Akses Jaringan (Firewall):** Batasi akses ke port SMB (445/tcp) agar hanya dapat dijangkau dari jaringan internal atau melalui VPN tepercaya, serta tutup akses langsung dari jaringan publik.
