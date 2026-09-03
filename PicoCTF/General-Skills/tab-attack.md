# writeups: picoCTF - Tab, Tab, Attack

## Ringkasan

Dokumen ini merupakan catatan (*write-up*) untuk tantangan *General Skills* bertajuk **Tab, Tab, Attack** di platform **picoCTF**. Tantangan ini dirancang untuk melatih efisiensi penggunaan terminal Linux, khususnya pemanfaatan fitur pelengkapan otomatis (*tab completion*), guna menavigasi struktur direktori berlapis yang sangat panjang dan rumit.

---

## Tools & Lingkungan Kerja

* **Platform Target:** picoCTF
* **Sistem Operasi:** Linux Terminal / Web Shell
* **Tools Utama:** 
  * `unzip` (untuk mengekstrak arsip)
  * Tombol `Tab` (untuk *Tab Completion* direktori)
  * Perintah navigasi dasar (`cd`, `ls`) dan eksekusi file biner

---

## Konsep Utama & Teori Dasar

1. **Tab Completion di Terminal**
   * Menekan tombol `Tab` pada keyboard memungkinkan terminal untuk secara otomatis melengkapi nama file atau direktori. Fitur ini sangat krusial untuk menghindari kesalahan ketik (*typo*) saat menghadapi struktur folder yang panjang, kompleks, dan acak.
2. **Eksekusi Berkas Biner (Executable)**
   * Berkas program atau skrip di dalam sistem Linux yang memerlukan izin/eksekusi langsung dengan sintaks `./` untuk memicu program tersebut berjalan dan menampilkan output (seperti flag).

---

## Langkah-langkah Penyelesaian (Step-by-Step)

### Tahap 1: Ekstraksi Berkas Arsip
Mengekstrak berkas arsip ZIP yang disediakan menggunakan perintah `unzip`:
```bash
unzip Addadshashanammu.zip
```
---

### Tahap 2: Navigasi Direktori Berlapis Menggunakan Tab Completion
Masuk ke dalam struktur folder yang sangat panjang dengan memanfaatkan tombol `Tab` pada keyboard untuk mempercepat pengetikan direktori bersarang:
```bash
cd Addadshashanammu/Almurbalarammi/Ashalmimilkalas/Assurnabitashpi/Maelkashishi/Onnissiralis/Ularradallaku
```
---

### Tahap 3: Menemukan dan Menjalankan Program File
Setelah berada di direktori terdalam, cek isi folder menggunakan perintah ls untuk melihat berkas biner program (misalnya `fang-of-haynekhtnamet`). Jalankan berkas tersebut dengan awalan `./`:
```bash
./fang-of-haynekhtnamet
```
___

### Tahap 4: Pengambilan Flag
Program akan langsung mengeksekusi perintah dan mencetak flag picoCTF ke layar terminal.


---


##Kesimpulan & Rekomendasi Pembelajaran
- Efisiensi Navigasi Terminal: Menguasai fitur tab completion (tombol Tab) menghemat waktu secara signifikan dan mencegah typo fatal saat bekerja dengan struktur direktori yang kompleks atau jalur (path) yang panjang di lingkungan Linux.
