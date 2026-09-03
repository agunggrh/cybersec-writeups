# Writeups : picoCTF 2022 - convertme.py

## Ringkasan

Dokumen ini merupakan catatan (*write-up*) untuk tantangan *General Skills* bertajuk **convertme.py** di platform **picoCTF**. Tantangan ini dirancang untuk menguji pemahaman dasar mengenai sistem bilangan komputer, khususnya konversi dari basis desimal ke basis biner, serta cara mengeksekusi dan berinteraksi dengan skrip otomatis berbasis bahasa Python.

---

## Tools & Lingkungan Kerja

* **Platform Target:** picoCTF (Beginner picoMini 2022)
* **Bahasa Pemrograman:** Python 3
* **Tools Utama:**
  * Terminal Linux / Web Shell (untuk mengeksekusi skrip)
  * Fungsi Bawaan Python `bin()` atau konverter angka (sebagai alat bantu hitung)

---

## Konsep Utama & Teori Dasar

1. **Sistem Bilangan Desimal dan Biner**
   * **Desimal (Basis 10):** Sistem angka harian yang menggunakan 10 simbol digit (0 sampai 9).
   * **Biner (Basis 2):** Sistem angka digital yang hanya menggunakan 2 simbol digit, yaitu `0` dan `1`. Komputer memproses seluruh data dalam bentuk biner.
2. **Eksekusi Skrip Interaktif**
   * Berinteraksi dengan program berbasis *command-line* yang meminta input pengguna secara dinamis berdasarkan nilai acak yang dihasilkan oleh program setiap kali dijalankan.

---

## Langkah-langkah Penyelesaian (Step-by-Step)

### Tahap 1: Pengunduhan Berkas Skrip
Mengunduh berkas skrip Python yang disediakan oleh tantangan menggunakan utilitas `wget` di dalam terminal:
```bash
wget [https://artifacts.picoctf.net/c/35/convertme.py](https://artifacts.picoctf.net/c/35/convertme.py)
```

---

### Tahap 2: Eksekusi Skrip Python
Menjalankan skrip menggunakan interpreter Python 3 untuk melihat pertanyaan acak yang diberikan oleh sistem:
```bash
python3 convertme.py
```

Contoh Tampilan Output Program:

If 100 is in decimal base, what is it in binary base?
Answer:

---

### Tahap 3: Konversi Desimal ke Biner
Mengubah angka desimal yang diberikan oleh program (misalnya angka 100) ke dalam bentuk biner. Proses ini dapat dilakukan menggunakan fungsi bawaan Python secara interaktif:

```bash
>>> bin(100)
'0b1100100'
```
Catatan: Hilangkan awalan 0b (yang menandakan format biner di Python), sehingga nilai yang diambil murni digit binernya saja, yaitu 1100100.

---

### Tahap 4: Pengiriman Jawaban dan Pengambilan Flag
- Memasukkan hasil konversi biner (contoh: 1100100) ke dalam kolom input Answer: pada terminal yang menjalankan skrip.
- Program memvalidasi jawaban, dan jika benar, teks keberhasilan beserta flag akan ditampilkan di layar.

---

### Kesimpulan & Pembelajaran
- Pemahaman Fondasi Komputer: Pemahaman mengenai konversi basis bilangan (desimal, biner, heksadesimal) adalah fondasi krusial dalam dunia keamanan siber dan reverse engineering.
- Pemanfaatan Python sebagai Kalkulator Cepat: Menggunakan interaktif shell Python sangat membantu dalam mempercepat proses perhitungan matematis atau konversi basis data tanpa harus menghitung secara manual atau bergantung pada situs luar.
