# PortSwigger Write-up: Unprotected admin functionality

## Ringkasan Tantangan

**Judul:** Unprotected admin functionality

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Kesulitan:** Apprentice

**Link Lab:** [https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality](https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality)

Tantangan ini bertujuan untuk menemukan panel administrasi yang tidak terlindungi dan mengeksploitasinya untuk menghapus pengguna bernama `carlos`. Kerentanan ini terjadi karena aplikasi mengandalkan kerahasiaan URL (*obfuscation*) sebagai bentuk keamanan, padahal jalur tersebut dapat ditemukan dengan mudah.

## Alat yang Digunakan

*   Peramban web (browser): Untuk menjelajahi aplikasi.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan menemukan URL yang tersembunyi, lalu mengaksesnya secara langsung.

1.  **Menemukan Petunjuk Admin:**
    *   Pergi ke URL lab dan tambahkan `/robots.txt` di belakangnya.
    *   File `robots.txt` berisi petunjuk tentang halaman yang tidak ingin diindeks oleh mesin pencari. Di sana, kami menemukan baris `Disallow: /administrator-panel`, yang menunjukkan alamat panel admin yang tersembunyi.

2.  **Mengakses Panel Admin:**
    *   Kami langsung mengakses URL `https://[lab-domain]/administrator-panel` di browser.
    *   Halaman admin terbuka tanpa memerlukan autentikasi apa pun, yang membuktikan adanya kerentanan kontrol akses.

3.  **Menghapus Pengguna `carlos`:**
    *   Di dalam panel admin, kami menemukan daftar pengguna.
    *   Kami mencari pengguna dengan nama `carlos` dan menggunakan fungsionalitas hapus yang tersedia untuk menghapusnya.

### Penjelasan Solusi

Tantangan ini berhasil dieksploitasi karena adanya celah *Broken Access Control*. Aplikasi gagal menerapkan validasi hak akses di sisi server, sehingga siapa pun yang mengetahui URL panel admin dapat mengaksesnya dan melakukan tindakan sensitif, seperti menghapus pengguna. Ini menunjukkan bahwa menyembunyikan URL bukanlah metode keamanan yang efektif.

## Saran Perbaikan

Untuk mencegah kerentanan semacam ini, implementasikan langkah-langkah berikut:

1.  **Validasi Hak Akses di Sisi Server:** Setiap kali permintaan untuk mengakses halaman admin diterima, server harus selalu memverifikasi apakah pengguna memiliki hak akses yang memadai.
2.  **Kontrol Akses Berbasis Peran:** Terapkan sistem kontrol akses berbasis peran (RBAC) untuk membatasi fungsionalitas hanya untuk pengguna yang berwenang.

## Bukti 

<img width="1920" height="1080" alt="Screenshot 2025-09-13 153032" src="https://github.com/user-attachments/assets/c458f10c-c4a5-4350-81e7-14b471d7fd02" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 153410" src="https://github.com/user-attachments/assets/237154fd-b06e-4ffd-844a-0de269ee22bf" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 153609" src="https://github.com/user-attachments/assets/db882e34-5996-402e-9787-29ec940d7014" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 153710" src="https://github.com/user-attachments/assets/4abda7ef-e787-4cd3-98a4-42cd4f96790c" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 153729" src="https://github.com/user-attachments/assets/569831d2-2a3a-47ea-9ffc-9d7b8aed1bc4" />
