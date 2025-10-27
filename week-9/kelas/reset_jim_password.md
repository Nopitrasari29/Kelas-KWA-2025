
# Juice-Shop Write-up: Reset Jim's Password

## Challenge Overview

**Title:** Reset Jim's Password
**Category:** OSINT
**Difficulty:** ⭐⭐⭐

Tantangan ini bertujuan untuk mereset kata sandi akun Jim dengan memanfaatkan kerentanan pada pertanyaan keamanan (*security question*) yang jawabannya mudah ditemukan di publik (OSINT) karena Jim adalah karakter fiksi.

## Tools Used

* **Web Browser:** Untuk mengakses halaman *reset* kata sandi dan pencarian informasi (simulasi OSINT).

## Metodologi dan Solusi

### 1. Identifikasi Karakter dan Pertanyaan Keamanan

Langkah awal adalah mengidentifikasi target email dan pertanyaan keamanan yang spesifik.

* **Aksi:** Mengakses halaman **Lupa Kata Sandi** (*Forgot Password*) dan memasukkan email target.
* **Email Target:** `jim@juice-sh.op`
* **Pertanyaan Keamanan:** "Your eldest siblings middle name?" (Apa nama tengah saudara tertua Anda?)

### 2. Melakukan OSINT (Pencarian Informasi)

Informasi dicari berdasarkan petunjuk bahwa 'Jim' merujuk pada **James Tiberius Kirk** dari *Star Trek*.

* **Pencarian:** Mencari informasi keluarga James T. Kirk.
* **Hasil OSINT:** Saudara tertua Jim Kirk adalah **George Samuel Kirk**.
* **Jawaban yang Relevan (Nama Tengah):** **`Samuel`**.

### 3. Mereset Kata Sandi (Penyelesaian)

Jawaban yang akurat digunakan untuk mengatasi kontrol keamanan.

* **Aksi:** Mengisi formulir *reset* kata sandi:
    * **Email:** `jim@juice-sh.op`
    * **Jawaban Keamanan:** **`Samuel`** (Nama tengah yang diminta)
    * **Kata Sandi Baru:** **`JimKirkNewP@ss`**
    * **Ulangi Kata Sandi:** **`JimKirkNewP@ss`**
* **Hasil:** Sistem menerima jawaban `Samuel` sebagai benar dan kata sandi Jim berhasil diubah, menyelesaikan *challenge*.

## Rekomendasi Keamanan

1.  **Validasi Pertanyaan:** Hindari pertanyaan keamanan yang jawabannya statis atau mudah ditemukan melalui sumber terbuka (OSINT).
2.  **Hapus Pertanyaan Keamanan:** Gantikan mekanisme *reset* ini dengan pengiriman *token reset* unik ke email pengguna.
3.  **Implementasi Multi-Faktor:** Terapkan autentikasi multi-faktor untuk melindungi akun, meskipun pertanyaan keamanan dibobol.

## Bukti 
<img width="278" height="342" alt="Screenshot 2025-10-27 194056" src="https://github.com/user-attachments/assets/90be9a88-6e6b-4634-b92e-1d599452d0fa" />

<img width="1920" height="1080" alt="Screenshot 2025-10-27 193915" src="https://github.com/user-attachments/assets/991966f1-f5b7-457c-82c1-faa63d6f0f4e" />

<img width="959" height="473" alt="Screenshot 2025-10-27 194211" src="https://github.com/user-attachments/assets/caad0ec9-318b-405d-afe6-91b5412ef809" />

