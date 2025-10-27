# Juice-Shop Write-up: Bjoern’s Favorite Pet

## Challenge Overview

**Title:** Bjoern’s Favorite Pet
**Category:** OSINT (Open Source Intelligence)
**Difficulty:** ⭐⭐⭐

Tantangan ini bertujuan untuk mendapatkan akses ke akun pengguna Bjoern dengan mengeksploitasi informasi pribadi yang tersedia secara publik (OSINT) untuk menjawab pertanyaan keamanan (*security question*) pada fitur *reset* kata sandi.

## Tools Used

* **Web Browser:** Untuk mengakses fitur *administration* dan halaman *reset* kata sandi.
* **Aplikasi Juice-Shop:** Sebagai target.

## Metodologi dan Solusi

### 1. Mendapatkan Email Bjoern

Langkah awal adalah mendapatkan alamat email Bjoern yang terdaftar di sistem.

* **Aksi:** Mengakses Panel Administrasi (*Administration*) melalui *endpoint* tersembunyi (misalnya, `/administration`).
* **Observasi:** Menganalisis daftar pengguna yang terdaftar.
* **Hasil (Email Target):** Ditemukan alamat email yang terkait dengan Bjoern untuk *reset* kata sandi, yaitu **`bjoern@owasp.org`** (berdasarkan *write-up* OSINT yang disimulasikan).

### 2. Menggunakan OSINT (Informasi Kunci)

Informasi untuk menjawab pertanyaan keamanan diperoleh melalui simulasi pencarian di media sosial (OSINT).

* **Simulasi Aksi OSINT:** Mencari akun Twitter Bjoern menggunakan email `bjoern@owasp.org`.
* **Simulasi Hasil:** Ditemukan *post* mengenai kucing kesayangannya.
* **Jawaban Pertanyaan Keamanan:** Nama hewan peliharaan adalah **`Zaya`**.

### 3. Melakukan Reset Kata Sandi

Informasi yang terkumpul digunakan pada halaman **Lupa Kata Sandi** (*Forgot Password*) di Juice-Shop.

* **URL Target:** Halaman *Forgot Password* (Misalnya, `http://localhost:3000/#/forgot-password`).
* **Pengisian Formulir:**
    * **E-mail:** `bjoern@owasp.org`
    * **Question de sécurité (Jawaban):** `Zaya`
    * **Nouveau mot de passe:** `MyNewP@ssw0rd123` (Kata sandi baru yang kita buat)
    * **Répétez le nouveau mot de passe:** `MyNewP@ssw0rd123`

* **Penyelesaian:** Setelah mengklik tombol **Changer**, *server* memverifikasi jawaban `Zaya` sebagai benar dan mereset kata sandi Bjoern. *Challenge* dianggap selesai.

## Rekomendasi Keamanan

Fitur Pertanyaan Keamanan rentan jika jawabannya mudah ditemukan di publik. Remediasi yang disarankan:

1.  **Hapus Pertanyaan Keamanan:** Sebaiknya ganti mekanisme *reset* dengan pengiriman tautan *reset* unik melalui email pengguna.
2.  **Edukasi Pengguna:** Dorong pengguna untuk membatasi informasi pribadi yang dibagikan di media sosial atau setidaknya membatasi akses melalui pengaturan privasi.

## Bukti
<img width="377" height="381" alt="Screenshot 2025-10-27 185902" src="https://github.com/user-attachments/assets/81e2930b-761a-4126-85fe-2ee40d848d25" />

<img width="288" height="372" alt="Screenshot 2025-10-27 190258" src="https://github.com/user-attachments/assets/19470e36-773f-4608-b811-f732d65d1f19" />

<img width="960" height="380" alt="Screenshot 2025-10-27 190317" src="https://github.com/user-attachments/assets/a5600bb5-6b3f-46be-bff9-5f557704be80" />

