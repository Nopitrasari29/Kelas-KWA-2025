# PortSwigger Write-up: Basic SSRF against another back-end system

**Lab Link:** [https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-backend-system](https://portswigger.net/web-security/ssrf/lab-basic-ssrf-against-backend-system)

## Overview

Lab ini bertujuan untuk mengeksploitasi kerentanan Server-Side Request Forgery (SSRF) pada fitur pemeriksaan stok. Eksploitasi ini memungkinkan server dipaksa untuk memindai *range* alamat IP pada jaringan *backend* yang terisolasi, menemukan antarmuka admin di *host* lain, dan mengeksekusi aksi sensitif, yaitu menghapus pengguna **`carlos`**.

## Detail Kerentanan

Aplikasi gagal membatasi alamat IP atau *hostname* yang dimasukkan ke parameter `stockApi`, memungkinkan *attacker* menggunakan server untuk melakukan pemindaian jaringan internal (Horizontal Privilege Escalation) terhadap *range* IP yang spesifik (`192.168.0.X`) pada port tertentu (`8080`).

## Tools

  * **Burp Suite Professional/Community Edition** (digunakan untuk *Proxy*, *Intruder*, dan *Repeater*).
  * **Peramban Web**.

## Langkah Penyelesaian

### 1\. Intercept Request Awal dan Identifikasi Parameter

  * Buka salah satu produk, lalu klik tombol **"Check stock"**.
  * **Intercept** *request* **POST** ini menggunakan Burp Proxy.
  * Kirim *request* tersebut ke **Burp Intruder** (Ctrl+I). Parameter yang rentan, yaitu `stockApi`, akan berisi URL yang dapat dimodifikasi.

### 2\. Konfigurasi Pemindaian Jaringan di Intruder

  * Pada tab **"Positions"**, ubah URL `stockApi` untuk menargetkan rentang IP internal dan port yang ditentukan oleh lab, sambil menyisakan ruang untuk *fuzzing* oktet terakhir:
    ```
    http://192.168.0.§1§:8080/admin
    ```
    > **Catatan:** Tandai angka `1` pada oktet terakhir sebagai posisi *payload* (**§**) di Intruder.
  * Pindah ke tab **"Payloads"**.
  * Atur **"Payload type"** menjadi **"Numbers"**.
  * Konfigurasi **Payload Options**:
      * **From:** `1`
      * **To:** `255`
      * **Step:** `1`

### 3\. Jalankan Serangan dan Temukan Host Admin

  * Klik **"Start attack"** dan tunggu hingga pemindaian selesai.
  * **Analisis** hasilnya di jendela *Intruder*. Sortir *response* berdasarkan kolom **"Status"** atau **"Length"**.
  * Host admin yang valid akan merespons dengan kode status yang berbeda dari *host* yang mati. Dalam lab ini, *host* admin merespons dengan status **200 OK** (atau panjang *response* yang signifikan).
  * Catat alamat IP yang berhasil ditemukan (misalnya, `192.168.0.42`).

### 4\. Eksploitasi Akhir dan Penghapusan Pengguna

  * Pilih *request* yang berhasil (status **200 OK**) dan kirim ke **Burp Repeater** (Ctrl+R).
  * Pada Repeater, ubah nilai parameter `stockApi` menggunakan IP yang telah ditemukan dan *endpoint* penghapusan pengguna:
    ```
    stockApi=http%3A%2F%2F192.168.0.42%3A8080%2Fadmin%2Fdelete%3Fusername%3Dcarlos
    ```
  * **Kirim** *request* (POST).
  * *Response* server akan mengonfirmasi penghapusan pengguna **`carlos`**, dan lab akan terpecahkan.

## Rekomendasi Solusi

Untuk memitigasi kerentanan SSRF yang memungkinkan pemindaian jaringan:

1.  **Validasi dan Sanitasi URL Input:**
      * Terapkan daftar putih (**Whitelisting**) yang ketat untuk *host* dan port yang diizinkan untuk diakses.
2.  **Pemblokiran Akses Jaringan Internal:**
      * Setelah URL di-resolve menjadi alamat IP, **secara eksplisit blokir** semua alamat IP pribadi dan *range* non-publik (misalnya, RFC 1918 seperti `192.168.X.X`, `10.X.X.X`, dll.).
3.  **Perkuat Kontrol Akses Internal:**
      * Pastikan *endpoint* administrasi dilindungi dengan autentikasi sesi yang kuat dan tidak dapat diakses hanya dengan *request* internal tanpa kredensial.

## Bukti
<img width="1920" height="1080" alt="Screenshot 2025-11-07 215505" src="https://github.com/user-attachments/assets/4f528c78-67f1-48bd-be2c-ae45d4296ac6" />

<img width="1920" height="1080" alt="Screenshot 2025-11-07 215627" src="https://github.com/user-attachments/assets/79b8855d-3d9b-42fd-8c93-b9377824a0e4" />

<img width="935" height="427" alt="Screenshot 2025-11-07 221608" src="https://github.com/user-attachments/assets/d350d66c-6ca4-46ca-9248-c07cda61458f" />

<img width="725" height="439" alt="Screenshot 2025-11-07 221810" src="https://github.com/user-attachments/assets/fbd8ac03-db83-493a-bf53-35aa46248894" />

<img width="1920" height="1080" alt="Screenshot 2025-11-07 221816" src="https://github.com/user-attachments/assets/6cffa8dd-3b29-4425-9813-8ff560e13443" />
