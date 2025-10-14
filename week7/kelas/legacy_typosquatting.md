# Juice-Shop Write-up: Legacy Typosquatting

## Challenge Overview

**Title:** Legacy Typosquatting
**Category:** Vulnerable Components / Cryptographic Issues
**Difficulty:** ⭐⭐⭐⭐ (4/6)

Tantangan ini mengharuskan kita untuk mengidentifikasi dan melaporkan *typosquatted package* (paket tiruan hasil salah ketik) yang digunakan sebagai dependensi lama dalam aplikasi Juice Shop, yang dapat menimbulkan risiko keamanan.

## Metodologi dan Solusi

### 1. Memahami Ancaman (Typosquatting)

* **Tujuan:** Memahami bahwa tantangan ini berfokus pada **Typosquatting**, yaitu teknik di mana penyerang membuat paket npm dengan nama yang sangat mirip dengan paket populer, berharap pengembang salah mengetik dan menginstal versi berbahaya.
* **Aksi:** Mengarahkan fokus penyelidikan pada daftar dependensi untuk mencari nama yang sedikit "menyimpang" dari paket standar.

### 2. Mengakses dan Menganalisis Developer's Backup

Langkah krusial pertama adalah mendapatkan daftar dependensi proyek.

* **Target File:** `package.json.bak` (Developer's Backup File).
* **Eksploitasi:** File ini berhasil diakses dan di-*download* (misalnya, melalui direktori `/ftp/` dengan menggunakan teknik *Poison Null Byte* `%2500` untuk *bypass* filter ekstensi).
* **Inspeksi:** Setelah file JSON diperoleh, perhatian difokuskan pada bagian `"dependencies"` untuk melihat setiap paket yang digunakan.

### 3. Penyelidikan Paket dan Identifikasi Pelaku

Setiap nama paket dalam daftar dependensi diperiksa di repositori npm (npmjs.com) untuk memastikan keabsahan dan popularitasnya.

| Dependensi | Pemeriksaan | Hasil |
| :--- | :--- | :--- |
| `express`, `sequelize`, `morgan`, dll. | Diperiksa di npm. | Paket standar dan umum digunakan. |
| **`epilogue-js`** | Dicari di npm. | **Mencurigakan:** Nama yang sangat mirip dengan paket populer (`epilogue`). |
| `z85` | Dicari di npm. | **Mencurigakan:** Versi lama, tetapi `epilogue-js` lebih mencolok. |

**Penemuan Kunci:**

Paket **`epilogue-js`** terbukti menjadi paket *typosquatted*. Halaman paket di npm secara eksplisit menampilkan pesan peringatan yang menandai keberadaannya *hanya untuk kesadaran dan pelatihan keamanan*, mengonfirmasi bahwa ia adalah tiruan dari paket asli **`epilogue`**.

### 4. Melaporkan Temuan untuk Menyelesaikan Challenge

Setelah paket *typosquatted* berhasil diidentifikasi, langkah terakhir adalah memberi tahu toko (aplikasi) tentang kerentanan ini sesuai instruksi tantangan.

* **Aksi:** Mengakses halaman **Customer Feedback** (atau Contact Us) di Juice Shop.
* **Penyelesaian:** Mengisi formulir dan secara eksplisit menyebutkan nama paket yang ditemukan di kolom komentar/pesan.

| Field | Input yang Menyelesaikan Tantangan |
| :--- | :--- |
| **Comment** | (Pesan yang mencantumkan nama paket) **`epilogue-js`** |

Dengan mengirimkan *feedback* yang berisi nama paket yang benar (**`epilogue-js`**), tantangan "Legacy Typosquatting" pun berhasil diselesaikan.

---

## Rekomendasi Keamanan

1.  **Audit Dependensi Otomatis:** Gunakan alat seperti Snyk atau `npm audit` secara rutin untuk memindai dependensi dari kerentanan dan paket yang dicurigai.
2.  **Verifikasi Sumber:** Selalu pastikan nama paket dieja dengan benar dan berasal dari *maintainer* resmi sebelum menginstalnya.
3.  **Kunci Versi:** Gunakan *lock file* (`package-lock.json` atau `yarn.lock`) untuk memastikan bahwa versi paket tidak berubah secara tidak terduga, melindungi dari *supply chain attack* di masa mendatang.

---

## Bukti 
<img width="1919" height="1079" alt="Screenshot 2025-10-14 122855" src="https://github.com/user-attachments/assets/059e81b4-d77b-428f-b981-c83424a9aa8b" />

<img width="639" height="928" alt="Screenshot 2025-10-14 123010" src="https://github.com/user-attachments/assets/4359a55e-e4ec-4a11-a3fc-b12b88178334" />

<img width="1920" height="1080" alt="Screenshot 2025-10-14 123258" src="https://github.com/user-attachments/assets/9d2ef2fd-32af-4c54-a99e-63231e9bc706" />

<img width="1920" height="1080" alt="Screenshot 2025-10-14 123317" src="https://github.com/user-attachments/assets/c7231460-5485-4ed9-862f-6fbc0436a496" />
