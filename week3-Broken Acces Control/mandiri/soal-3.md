# PortSwigger Write-up: URL-based access control can be circumvented

## Ringkasan Tantangan

**Judul:** URL-based access control can be circumvented

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Kesulitan:** Apprentice

**Link Lab:** [https://portswigger.net/web-security/access-control/lab-url-based-access-control-can-be-circumvented](https://portswigger.net/web-security/access-control/lab-url-based-access-control-can-be-circumvented)

Tantangan ini bertujuan untuk melewati kontrol akses yang diterapkan di lapisan front-end dengan memanipulasi header HTTP. Tujuannya adalah untuk mengakses panel administrasi dan menghapus pengguna bernama `carlos`.

## Alat yang Digunakan

*   **Burp Suite Community Edition:** Digunakan untuk memodifikasi dan mengirim ulang permintaan HTTP menggunakan fitur Repeater.

## Metodologi dan Solusi

Tantangan ini berhasil diselesaikan dengan mengeksploitasi celah di mana server back-end memproses URL dari header non-standar, bukan dari permintaan asli.

1.  **Mengidentifikasi Kontrol Akses:**
    *   Saat mencoba mengakses URL `/admin` secara langsung, akses diblokir oleh front-end.
    *   Respons yang diterima menunjukkan bahwa halaman tidak ditemukan, yang mengisyaratkan bahwa permintaan tidak pernah sampai ke server back-end.

2.  **Menggunakan Burp Repeater:**
    *   Kirim permintaan awal (misalnya, saat mencoba mengakses `/admin`) ke Burp Repeater.
    *   Di Repeater, ubah jalur URL di baris `GET` dari `/admin` menjadi `/` saja.
    *   Tambahkan header HTTP baru bernama `X-Original-URL` dengan nilai `/admin`.
    *   Kirim permintaan tersebut. Server back-end akan memproses header `X-Original-URL`, mengabaikan URL asli, dan memberikan akses ke halaman admin.

3.  **Menghapus Pengguna `carlos`:**
    *   Setelah halaman admin terbuka, ubah kembali permintaan di Repeater untuk menghapus pengguna.
    *   Di baris `GET`, tambahkan query string untuk menentukan pengguna yang akan dihapus: `/?username=carlos`.
    *   Ubah nilai header `X-Original-URL` menjadi jalur untuk fungsi penghapusan: `/admin/delete`.
    *   Kirim permintaan ini. Server akan memprosesnya dan menghapus pengguna `carlos`.

### Penjelasan Solusi

Kerentanan ini terjadi karena adanya mis-konfigurasi kontrol akses. Alih-alih memvalidasi hak akses di sisi server, aplikasi bergantung pada front-end atau reverse proxy untuk memblokir akses ke URL sensitif. Dengan memanipulasi header `X-Original-URL`, yang sering digunakan untuk komunikasi internal, penyerang bisa melewati mekanisme perlindungan ini dan menjalankan fungsi admin secara langsung.

## Saran Perbaikan

Untuk mencegah kerentanan semacam ini, implementasikan langkah-langkah berikut:

1.  **Validasi Hak Akses di Sisi Server:** Selalu validasi hak akses pada setiap permintaan di sisi server (back-end), bukan hanya di lapisan front-end.
2.  **Filter Header:** Konfigurasi server untuk mengabaikan atau membersihkan header yang tidak valid, terutama yang dapat digunakan untuk mengontrol routing

## Bukti 

<img width="1920" height="1080" alt="Screenshot 2025-09-13 162102" src="https://github.com/user-attachments/assets/05e99383-d370-40e4-ba40-209f6a9e3f5d" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 162907" src="https://github.com/user-attachments/assets/5a5b8c60-de1d-4ecc-9e6e-42bb2ede3783" />

<img width="1335" height="528" alt="Screenshot 2025-09-13 164127" src="https://github.com/user-attachments/assets/bc4e5762-bd88-4e2a-b2dc-4a91814d4004" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 164246" src="https://github.com/user-attachments/assets/c1190be4-ebb0-457d-a5cc-fa8c6ec4b111" />



