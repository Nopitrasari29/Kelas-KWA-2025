# PortSwigger Write-up: Basic SSRF against the local server 

#### Lab Link : https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-localhost
## Overview

Lab ini mendemonstrasikan kerentanan Server-Side Request Forgery (SSRF) dasar yang ditemukan pada fitur pemeriksaan stok. Kerentanan ini memungkinkan server dipaksa untuk membuat *request* ke sumber daya internal yang terlarang, yaitu antarmuka administrasi lokal, sehingga *attacker* dapat mengeksekusi aksi penghapusan pengguna tanpa otorisasi.

## Detail Kerentanan

Aplikasi gagal memvalidasi atau membatasi *input* URL yang dimasukkan pengguna ke parameter `stockApi`. Hal ini memungkinkan penyertaan alamat *loopback* (`127.0.0.1`), yang berhasil mem-*bypass* kontrol akses berbasis *hostname* (`localhost`) dan mengizinkan *request* internal untuk mengakses *endpoint* `/admin`.

## Tools

  * Burp Suite Professional/Community Edition (untuk *Proxy* dan *Repeater*).
  * Peramban Web.

## Langkah Penyelesaian

1.  **Intercept Request Awal:** Tangkap *request* **POST** dari fitur "Check stock" menggunakan Burp Proxy dan kirim ke Repeater.
2.  **Akses Antarmuka Admin:** Modifikasi parameter `stockApi` untuk menargetkan antarmuka admin lokal menggunakan alamat IP *loopback* untuk menghindari filter awal:
    ```
    stockApi=http%3A%2F%2F127.0.0.1%2Fadmin
    ```
3.  **Pengambilan Informasi:** Kirim *request* dan analisis *response* (HTTP 200 OK) untuk menemukan URL penghapusan pengguna: `/admin/delete?username=carlos`.
4.  **Eksploitasi Akhir:** Modifikasi kembali parameter `stockApi` untuk mengeksekusi fungsi penghapusan secara langsung:
    ```
    stockApi=http%3A%2F%2F127.0.0.1%2Fadmin%2Fdelete%3Fusername%3Dcarlos
    ```
5.  **Validasi:** Kirim *request* akhir (POST). Server akan mengeksekusi perintah penghapusan secara internal, dan lab akan terpecahkan.

## Rekomendasi Solusi

Untuk memitigasi kerentanan SSRF pada fungsi server-to-server ini, disarankan untuk fokus pada tiga langkah mitigasi utama:

1.  **Implementasi Whitelisting URL (Mitigasi Primer):**
      * Terapkan daftar putih yang ketat yang hanya mengizinkan *hostname* atau alamat IP eksternal yang diizinkan untuk diakses. Semua URL lainnya harus ditolak secara default.
2.  **Pemblokiran Jaringan Internal:**
      * Secara tegas blokir akses ke alamat IP *loopback* (`127.0.0.1`, `localhost`) dan semua *range* alamat IP pribadi (RFC 1918). Validasi ini harus dilakukan setelah *hostname* dinormalisasi menjadi alamat IP.
3.  **Perkuat Kontrol Akses Internal:**
      * Pastikan *endpoint* sensitif, seperti `/admin`, dilindungi oleh mekanisme otentikasi sesi yang kuat. *Endpoint* tersebut tidak boleh dapat diakses hanya dengan mengakses URL secara langsung, bahkan dari *loopback*.

## Bukti 
<img width="716" height="447" alt="Screenshot 2025-11-07 095712" src="https://github.com/user-attachments/assets/9180f1ec-765b-4414-a974-ce5f10cc43d9" />

<img width="732" height="504" alt="Screenshot 2025-11-07 100156" src="https://github.com/user-attachments/assets/91613331-f24a-401b-b366-bd370695eb01" />

<img width="728" height="481" alt="Screenshot 2025-11-07 100843" src="https://github.com/user-attachments/assets/1603d572-c716-4aa6-8e15-5fe9a6dac4f5" />

<img width="736" height="428" alt="Screenshot 2025-11-07 101212" src="https://github.com/user-attachments/assets/e616ba91-f3a7-4bc1-a397-fc298ea5d13d" />

<img width="1920" height="1080" alt="Screenshot 2025-11-07 101224" src="https://github.com/user-attachments/assets/e774a3d7-2872-496d-9893-62f4330ccfb7" />
