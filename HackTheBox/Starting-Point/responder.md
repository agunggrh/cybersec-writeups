# Write-up: Responder (Hack The Box - Starting Point)

## Ringkasan
Dokumen ini merupakan catatan penetrasi (write-up) untuk modul **Unika** di platform Hack The Box (HTB). Lab ini dirancang untuk mengajarkan eksploitasi kerentanan *File Inclusion* (LFI/RFI) guna mengekstrak informasi sensitif, memahami mekanisme pengumpulan kredensial, serta memanfaatkan akses *Remote Administration* melalui WinRM untuk mendapatkan flag.

## Tools & Lingkungan Kerja
- **Target IP:** `<IP_Target>`
- **Tools Utama:** 
  - **Browser / Curl** (Pengujian parameter *File Inclusion*)
  - **Evil-WinRM** (Klien shell jarak jauh berbasis protokol WinRM untuk administrasi Windows)

## Konsep Utama & Teori Dasar
1. **File Inclusion Vulnerability (LFI/RFI)**: Cela keamanan pada aplikasi web yang memperbolehkan pengguna memuat atau menyertakan file lokal maupun jarak jauh ke dalam fungsi eksekusi kode (seperti `include` di PHP).
2. **WinRM (Windows Remote Management)**: Protokol manajemen jarak jauh bawaan Microsoft yang berjalan pada port TCP `5985` (HTTP) atau `5986` (HTTPS) untuk administrasi sistem berbasis *command-line*.

## Langkah-langkah Penetrasi (Step-by-Step)

### Tahap 1: Analisis Kerentanan File Inclusion
Pengujian awal mengidentifikasi adanya parameter `page` pada aplikasi web yang rentan terhadap manipulasi direktori atau pemanggilan file eksternal:
```text
http://unika.htb/index.php?page=...
```
Melalui pengujian parameter tersebut, aplikasi web memunculkan pesan *error* fungsi `include` PHP, yang mengonfirmasi bahwa input pengguna diproses langsung oleh sistem file server.

### Tahap 2: Pengumpulan Kredensial & Eksploitasi Lanjutan
Berdasarkan konteks lab dan hasil penelusuran celah keamanan, didapatkan kredensial akses untuk akun *administrator* atau *user* terkait pada sistem target, di mana password yang ditemukan adalah `badminton`.

### Tahap 3: Remote Administration via WinRM
Mengetahui bahwa port pengelolaan jarak jauh WinRM (`5985`) terbuka pada target, akses sistem dilakukan menggunakan *tool* `evil-winrm` dengan kredensial yang valid:
```bash
evil-winrm -i 10.129.95.108 -u administrator -p badminton
```

### Tahap 4: Pencarian dan Pengambilan Flag
Setelah berhasil masuk ke dalam sistem target melalui sesi *Evil-WinRM*, navigasi dilakukan ke direktori *desktop* user terkait (`mike` / `administrator`) untuk mencari dan membaca file flag:
```powershell
cd C:\Users\mike\Desktop
dir
type user.txt
```
*String* flag berformat `HTB{...}` berhasil ditemukan dan disalin.

## Kesimpulan & Rekomendasi Keamanan (Remediation)
- **Validasi Input (*Input Validation*):** Terapkan daftar putih (*whitelist*) ketat pada parameter halaman atau file yang dapat di-*include* untuk mencegah eksekusi file arbitrer (LFI/RFI).
- **Nonaktifkan Fitur Insecure PHP:** Pastikan direktif `allow_url_include` dimatikan (`Off`) di dalam konfigurasi `php.ini` untuk mencegah *Remote File Inclusion*.
- **Batasi Akses WinRM:** Batasi akses port WinRM (`5985`/`5986`) hanya dari alamat IP administrator tepercaya menggunakan Windows Firewall.
