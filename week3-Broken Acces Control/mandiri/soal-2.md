# PortSwigger Write-up: Unprotected admin functionality with unpredictable URL

## Ringkasan Tantangan

**Judul:** Unprotected admin functionality with unpredictable URL

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Kesulitan:** Apprentice

**Link Lab:** [https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality-with-unpredictable-url](https://portswigger.net/web-security/access-control/lab-unprotected-admin-functionality-with-unpredictable-url)

Tantangan ini bertujuan untuk menemukan panel administrasi yang tidak terlindungi, meskipun alamat URL-nya tidak bisa ditebak. Petunjuknya tersembunyi di dalam kode sumber halaman web, dan tujuannya adalah untuk menghapus pengguna bernama `carlos`.

## Alat yang Digunakan

*   Peramban web (browser): Untuk menjelajahi aplikasi dan memeriksa kode sumbernya.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan meninjau kode sumber di sisi klien untuk menemukan URL tersembunyi.

1.  **Mencari Petunjuk di Kode Sumber:**
    *   Pergi ke URL lab dan buka kode sumber halaman dengan menekan `Ctrl + U` (Windows/Linux) atau `Cmd + Option + U` (Mac).
    *   Di dalam kode, ditemukan file JavaScript bernama `main.js`.
    *   Dengan memeriksa file `main.js`, ditemukan baris kode yang mendefinisikan rute untuk panel admin, seperti `path: '/admin-peoafu'`.

2.  **Mengakses Panel Admin:**
    *   Menggunakan alamat yang ditemukan, navigasi ke URL `https://[lab-domain]/admin-peoafu`.
    *   Halaman admin terbuka tanpa memerlukan autentikasi, yang membuktikan adanya kerentanan.

3.  **Menghapus Pengguna `carlos`:**
    *   Di halaman panel admin, cari dan hapus pengguna dengan nama `carlos`.

### Penjelasan Solusi

Tantangan ini berhasil dieksploitasi karena adanya celah *Broken Access Control*. Aplikasi gagal menerapkan validasi hak akses di sisi server, dan hanya mengandalkan URL yang kompleks atau "tidak tertebak" sebagai satu-satunya bentuk perlindungan. Penyerang dapat dengan mudah menemukan URL ini dengan menganalisis kode sumber, sehingga bisa mengakses fungsionalitas sensitif secara langsung.

## Saran Perbaikan

Untuk mencegah kerentanan semacam ini, implementasikan langkah-langkah berikut:

1.  **Validasi Hak Akses di Sisi Server:** Setiap permintaan untuk mengakses halaman admin harus selalu diverifikasi oleh server untuk memastikan pengguna memiliki hak akses yang memadai.
2.  **Jalur URL yang Dilindungi:** Jangan pernah mengandalkan kerahasiaan URL untuk mengamankan fungsionalitas sensitif.

## Bukti 

<img width="1920" height="1080" alt="Screenshot 2025-09-13 154301" src="https://github.com/user-attachments/assets/4a5b8cab-8070-4568-8831-896c7a79581c" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 154356" src="https://github.com/user-attachments/assets/2801e2ab-f85c-44ba-b7b6-36c773015085" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 154439" src="https://github.com/user-attachments/assets/afefb43b-ae9e-41eb-9f5e-9a98f4117cbd" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 154809" src="https://github.com/user-attachments/assets/ad8e1a45-e436-4ea5-999e-b81c1514d0e7" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 154819" src="https://github.com/user-attachments/assets/b05cfb5e-f08a-4db2-9882-b1f06880f51c" />

