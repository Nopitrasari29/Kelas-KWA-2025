# Juice-Shop Write-up: Frontend Typosquatting

## Challenge Overview

**Title:** Frontend Typosquatting
**Category:** Vulnerable Components / Insecure Deserialization
**Difficulty:** ⭐⭐⭐⭐⭐ (5/6)

Tantangan ini menargetkan praktik *typosquatting* pada dependensi *frontend* yang dibangun dengan Angular. Tujuannya adalah menemukan paket dengan nama yang salah eja di daftar lisensi dan melaporkannya.

## Metodologi dan Solusi

### 1. Mengakses Informasi Lisensi Frontend

Langkah awal adalah mencari tahu daftar *library* yang digunakan di sisi *frontend* aplikasi.

* **Aksi:** Mengakses file **`3rdpartylicenses.txt`** melalui *URL* publik Juice Shop (`http://[IP]/3rdpartylicenses.txt`).
* **Tujuan:** Mendapatkan daftar lengkap semua *library* pihak ketiga beserta informasi lisensinya.

### 2. Audit Manual dan Identifikasi Typo Baru

Berdasarkan pengalaman dari *challenge* *typosquatting* sebelumnya, dan menyadari bahwa jawaban yang umum (**`anuglar2-qrcode`**) mungkin sudah usang untuk versi Juice Shop ini, audit manual yang cermat dilakukan pada seluruh isi file `3rdpartylicenses.txt`.

* **Penyelidikan Awal:** Pencarian kata kunci seperti `anuglar2-qrcode` gagal, menandakan perlunya mencari *typo* baru.
* **Penemuan Kunci:** Setelah memindai seluruh daftar, ditemukan paket yang namanya aneh dan tidak mengikuti konvensi penamaan umum untuk *library* Angular: **`ngy-cookie`**.
* **Analisis:** Nama ini sangat mirip dengan paket manajemen *cookie* yang seharusnya (`ngx-cookie` atau `ng-cookie`). Kesalahan substitusi huruf 'y' untuk 'x' atau 'c' di awal nama paket ini adalah indikasi kuat dari praktik *typosquatting*.

### 3. Pelaporan Kerentanan (Responsible Disclosure)

Setelah paket *typosquatted* yang baru (**`ngy-cookie`**) berhasil diidentifikasi, tantangan diselesaikan dengan melaporkan temuan tersebut melalui mekanisme *feedback* aplikasi.

* **Aksi:** Menuju halaman **Customer Feedback** / **Contact Us** di Juice Shop.
* **Isi Feedback:** Laporan dikirim dengan fokus tunggal pada nama paket yang ditemukan untuk memastikan validasi oleh sistem.

| Field | Isi yang Digunakan |
| :--- | :--- |
| **Comment/Message** | "Saya menemukan *library* di *frontend* yang namanya salah eja. Paket yang terdaftar sebagai **`ngy-cookie`** kemungkinan besar adalah paket *typosquatted* yang meniru paket manajemen *cookie* yang sah." |

* **Hasil:** Laporan tersebut diterima, memvalidasi **`ngy-cookie`** sebagai paket *typosquatted* di *frontend* untuk versi Juice Shop ini, dan *challenge* berhasil diselesaikan.

## Kesimpulan dan Implikasi Keamanan

Tantangan ini menunjukkan bahwa teknik *typosquatting* tidak hanya menargetkan *backend* (seperti yang terlihat pada `epilogue-js`), tetapi juga *frontend*. Hal ini juga menegaskan pentingnya tidak hanya mengandalkan *write-up* lama, tetapi juga melakukan audit manual yang cermat, terutama ketika menjalankan versi aplikasi yang lebih baru.

* Pengembang harus selalu memverifikasi ejaan dan sumber semua dependensi, baik untuk *backend* maupun *frontend*.
* Penggunaan *scanning tool* otomatis sangat disarankan untuk mendeteksi anomali penamaan dan versi.

---

## Bukti
<img width="766" height="934" alt="image" src="https://github.com/user-attachments/assets/fadbbc9b-9fbf-4117-9bf1-b8db16bd7fdf" />

<img width="1920" height="1080" alt="Screenshot 2025-10-14 130127" src="https://github.com/user-attachments/assets/1efa1018-6f40-4be9-b040-af657db66001" />

<img width="1920" height="1080" alt="Screenshot 2025-10-14 130135" src="https://github.com/user-attachments/assets/cbbd26e8-50b2-492f-9e1e-cf7487b30197" />
