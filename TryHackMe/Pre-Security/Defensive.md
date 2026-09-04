# THM-Defensive-Security-Writeup

Write-up ini mendokumentasikan langkah-langkah penyelesaian modul investigasi dan penanganan insiden *Defensive Security* di platform **TryHackMe**. Dalam investigasi ini, kita membantu seorang analis SOC magang bernama Joe untuk mendeteksi, mengidentifikasi, dan menghentikan serangan cyber terhadap aplikasi web perbankan (`fakebank.com`).

---

## Ringkasan Kasus (Case Summary)

| Parameter | Detail |
| :--- | :--- |
| **Peran** | SOC Analyst (Junior/Intern Helper) |
| **Skenario** | Deteksi & Penanganan Serangan Web Reconnaissance (Directory Bruteforcing) |
| **Target System** | `fakebank.com` |
| **Attacker IP** | `32.122.195.63` |
| **Jenis Serangan** | URL Discovery / Directory Enumeration |
| **Target Penyerang** | Endpoint sensitif (`https://fakebank.com/admin`) |
| **Flag Akhir** | `THM{FAKEBANK-SECURED}` |

---

## Task 1: Mendeteksi Aktivitas Mencurigakan (Detecting Suspicious Activity)

### Latar Belakang
Joe sedang menjalankan shift solo pertamanya sebagai Analis SOC. Dasbor pemantauan (*monitoring dashboard*) menunjukkan adanya anomali lalu lintas jaringan. Tugas kita adalah memeriksa peringatan (*alerts*) terbaru dan mengidentifikasi IP sumber yang bertanggung jawab atas aktivitas mencurigakan tersebut.

### Langkah Penyelesaian
1. Mengakses dasbor pemantauan (*Monitoring Dashboard*).
2. Memeriksa log lalu lintas dan peringatan terbaru (*recent alerts*).
3. Mengidentifikasi alamat IP asal (*Source IP*) yang menghasilkan lonjakan *request* atau aktivitas tidak wajar.

### Pertanyaan & Jawaban
* **Pertanyaan:** *Alamat IP sumber mana yang menghasilkan lalu lintas mencurigakan tersebut?*
* **Jawaban:** `32.122.195.63`

---

## Task 2: Mengidentifikasi Serangan (Identifying the Attack)

### Latar Belakang
Setelah mengetahui IP penyerang, kita perlu menganalisis jenis serangan yang sedang terjadi. Berdasarkan pola log pada dasbor, penyerang melakukan *upaya penemuan URL* (*URL Discovery / Directory Bruteforcing*) secara berulang dan cepat untuk mencari halaman tersembunyi atau area administrasi.

### Langkah Penyelesaian
1. Membuka bagian daftar **"Upaya Penemuan URL"** (*URL Discovery Attempts*) di dasbor pemantauan.
2. Mengurutkan atau melihat entri terbaru dari aktivitas penyerang (`32.122.195.63`).
3. Mengidentifikasi URL/endpoint sensitif yang menjadi target utama penyerang.

### Pertanyaan & Jawaban
* **Pertanyaan:** *Salin URL terbaru yang coba dicari oleh penyerang dan tempelkan di bawah ini.*
* **Jawaban:** `https://fakebank.com/admin`

---

## Task 3: Hentikan Serangan (Stopping the Attack)

### Latar Belakang
Langkah utama dalam keamanan defensif saat terjadi insiden adalah **Containment** (penahanan/pemutusan akses) untuk menghentikan dampak serangan secara real-time sebelum dilakukan penanganan kerentanan (*remediation*).

Beberapa tindakan mitigasi yang diterapkan:
1. **Memblokir Alamat IP (IP Blocking):** Mencegah akses penuh dari perangkat penyerang.
2. **Penerapan Pembatasan Laju (Rate Limiting):** Membatasi frekuensi *request* untuk mencegah *bruteforce* atau *Denial of Service* (DoS).
3. **Memperbarui Aturan Keamanan (Updating Security Rules):** Memperketat *access control* pada halaman sensitif `/admin`.

### Langkah Penyelesaian
1. Membuka menu konfigurasi **Firewall Rules** pada dasbor.
2. Memasukkan IP penyerang: `32.122.195.63`.
3. Memilih tindakan: **`BLOCK`** dari menu drop-down.
4. Menekan tombol **Apply / Terapkan** untuk mengaktifkan aturan firewall baru.
5. Mendapatkan pesan sukses beserta flag bukti penanganan insiden.

### Pertanyaan & Jawaban
* **Pertanyaan:** *Saat pesan sukses muncul, salin flag tersebut dan tempelkan di bawah ini.*
* **Jawaban:** `THM{FAKEBANK-SECURED}`

---

## Pelajaran yang Dipelajari (Key Takeaways)

1. **Monitoring & Alerting:** Dasbor SOC yang efektif sangat penting untuk mendeteksi *anomalous behavior* sejak dini.
2. **Directory Enumeration Awareness:** Aktivitas berupa permintaan URL secara massal dan cepat biasanya mengindikasikan penggunaan tools *automated discovery* seperti Gobuster, Dirbuster, atau ffuf.
3. **Incident Response Lifecycle:**
   - **Detection** (Task 1: Identifikasi IP Sumber)
   - **Analysis** (Task 2: Menentukan Vektor Serangan / Target URL)
   - **Containment & Remediation** (Task 3: Pemblokiran via Firewall & Rate Limiting)
