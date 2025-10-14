# Juice-Shop Write-up: Vulnerable Library

## Challenge Overview

**Title:** Vulnerable Library
**Category:** Vulnerable Components
**Difficulty:** ⭐⭐⭐⭐ (4/6)

Tantangan ini bertujuan untuk mengedukasi tentang pentingnya audit dependensi. Kita diminta untuk mengidentifikasi *library* yang digunakan Juice Shop yang memiliki kerentanan yang diketahui, dan melaporkannya.

## Tools Used

  * **File Developer's Backup (`package.json.bak`)**: Sumber data dependensi.
  * **Web Browser**: Digunakan untuk mengakses aplikasi Juice Shop dan formulir *feedback*.

## Metodologi dan Solusi

### 1\. Analisis Sumber Dependensi

Langkah pertama adalah mendapatkan daftar lengkap semua *library* yang digunakan Juice Shop serta nomor versinya.

  * **Aksi:** Mengakses file *backup* **`package.json.bak`** (misalnya, melalui direktori `/ftp/` dengan teknik *bypass* file ekstensi).
  * **Tujuan:** Memperoleh daftar dependensi untuk di-*audit*.

### 2\. Identifikasi Perpustakaan (Library) yang Rentan

Setelah mendapatkan file backup, pemeriksaan fokus pada dependensi yang diketahui rentan pada versi lama.

  * **Pencarian di `package.json.bak`:** Saya mencari paket yang berhubungan dengan keamanan atau autentikasi dan menemukan entri:
    ```json
    "express-jwt": "0.1.3",
    ```
  * **Konfirmasi Kerentanan:** Berdasarkan basis data kerentanan yang ada (seperti yang ditunjukkan dalam petunjuk tantangan), *library* **`express-jwt`** diketahui rentan terhadap *Authorization Bypass* pada **versi di bawah `< 6.0.0`**.
  * **Kesimpulan:** Karena Juice Shop menggunakan versi **`0.1.3`**, yang jauh di bawah ambang batas yang aman, *library* ini **dikonfirmasi** sebagai komponen yang rentan.

### 3\. Pelaporan Kerentanan dan Penyelesaian Challenge

Sesuai dengan praktik *responsible disclosure* (pengungkapan yang bertanggung jawab) yang disimulasikan, tantangan ini diselesaikan dengan melaporkan temuan kepada manajemen toko melalui fitur *feedback*.

  * **Aksi:** Saya menavigasi ke halaman **Customer Feedback** aplikasi Juice Shop.

  * **Isi Feedback:** Saya memasukkan pesan yang secara eksplisit menyebutkan nama paket yang rentan dan versinya:

    | Field | Isi yang Digunakan |
    | :--- | :--- |
    | **Comment/Message** | "Saya menemukan bahwa aplikasi ini menggunakan paket **`express-jwt`** versi **`0.1.3`**. Versi ini sangat rentan terhadap kerentanan **Authorization Bypass** dan harus di-*upgrade* secepatnya ke versi 6.0.0 atau lebih tinggi." |

  * **Hasil:** Setelah pesan dikirim, sistem Juice Shop memvalidasi nama paket yang rentan tersebut, dan *challenge* **"Vulnerable Library"** berhasil diselesaikan.

## Rekomendasi Keamanan

Penyelesaian tantangan ini menekankan bahwa kerentanan bisa masuk melalui kode pihak ketiga. Solusinya adalah:

1.  **Prioritaskan *Upgrade***: Segera *upgrade* dependensi penting seperti `express-jwt` ke versi terbaru (minimal 6.0.0 ke atas) untuk menerapkan *patch* keamanan.
2.  **Audit Otomatis:** Terapkan alat audit dependensi (seperti Snyk atau fungsi `npm audit`) sebagai bagian rutin dari proses *build* untuk mendeteksi kerentanan yang diketahui secara otomatis.
3.  **Kunci Versi:** Pastikan *lock file* (`package-lock.json`) selalu diperbarui untuk menghindari penginstalan versi lama yang rentan.

----
## Bukti 
<img width="323" height="261" alt="Screenshot 2025-10-14 123947" src="https://github.com/user-attachments/assets/258fb4c6-c121-4ada-ae71-b2c5e32dd0f5" />

<img width="1919" height="1006" alt="Screenshot 2025-10-14 124115" src="https://github.com/user-attachments/assets/dbd2c2d0-f847-4763-aa49-546e66504a55" />

<img width="1920" height="1080" alt="Screenshot 2025-10-14 124123" src="https://github.com/user-attachments/assets/1d18212b-5001-443b-8664-8be332308394" />
