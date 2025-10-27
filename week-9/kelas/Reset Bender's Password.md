# Juice-Shop Write-up: Reset Bender's Password

## Challenge Overview

**Title:** Reset Bender's Password
**Category:** Broken Authentication / OSINT
**Difficulty:** ⭐⭐⭐⭐

Tantangan ini diselesaikan dengan memanfaatkan kecerobohan keamanan di fitur *reset* kata sandi. Jawabannya ditemukan melalui *Open Source Intelligence* (OSINT) pada karakter fiksi **Bender** dari serial **Futurama**.

## Tools Used

* **Web Browser:** Untuk mengakses fitur **Lupa Kata Sandi** (*Forgot Password*).

---

## Metodologi dan Solusi

### 1. Identifikasi Target dan Pertanyaan

Langkah pertama adalah menentukan target dan pertanyaan keamanan yang harus dijawab.

* **Email Target:** `bender@juice-sh.op`
* **Pertanyaan Keamanan:** "Company you first work for as an adult?"

### 2. Melakukan OSINT pada Karakter Fiksi

* **Aksi:** Melakukan pencarian informasi publik mengenai **Bender Bending Rodriguez** (karakter dari *Futurama*).
* **Pencarian Fokus:** Pekerjaan pertama Bender saat dewasa. Diketahui Bender bekerja di pabrik yang membuat bilik bunuh diri (*suicide booths*).
* **Verifikasi Jawaban:** Melalui pencarian silang di sumber-sumber *Futurama* (*wiki*), ditemukan bahwa nama merek dagang yang digunakan untuk produk bilik bunuh diri tersebut adalah **`Stop'n'Drop`**. Jawaban ini yang diterima oleh sistem Juice-Shop.

### 3. Mereset Kata Sandi

Jawaban yang ditemukan digunakan pada formulir *reset password*.

* **Aksi:** Mengakses halaman **Forgot Password** (setelah *logout* dari akun Bender).
* **Data yang Digunakan:**
    * **Email:** `bender@juice-sh.op`
    * **Jawaban Keamanan:** **`Stop'n'Drop`**
    * **Kata Sandi Baru:** Kamu memasukkan kata sandi baru (misalnya, `NewBenderP@ss`).
* **Penyelesaian:** Dengan *reset* yang berhasil, *challenge* selesai.

---

## Rekomendasi Keamanan

1.  **Hapus Pertanyaan Keamanan:** Mekanisme *reset* *password* harus menggunakan pengiriman tautan unik ke email pengguna, bukan pertanyaan keamanan yang jawabannya statis.
2.  **Jawablah secara Subjektif:** Jika pertanyaan keamanan terpaksa digunakan, jawabannya harus bersifat subjektif atau hanya diketahui oleh pengguna (misalnya, nama panggilan masa kecil yang tidak pernah digunakan di media sosial).

## Bukti

<img width="282" height="320" alt="Screenshot 2025-10-27 211052" src="https://github.com/user-attachments/assets/92c5bebe-b236-418f-80fe-468b79cc4afa" />

<img width="1920" height="1080" alt="Screenshot 2025-10-27 211323" src="https://github.com/user-attachments/assets/0ac48eba-537a-467f-bdbd-4ae72d29d222" />

<img width="1920" height="1080" alt="Screenshot 2025-10-27 211539" src="https://github.com/user-attachments/assets/0e746eb8-ec91-4251-b325-2619b23077b9" />
