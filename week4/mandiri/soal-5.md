# Write-up: JWT Authentication Bypass via JKU Header Injection

**Link Lab:** `https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-jku-header-injection`

## Tujuan (Goal)

Mengeksploitasi fitur *JSON Web Key Set URL* (`jku`) pada JWT. Server yang salah konfigurasi akan mengambil *public key* yang kita sediakan melalui URL eksternal (`jku`) untuk memverifikasi tanda tangan. Ini memungkinkan penyerang menandatangani token administrator palsu menggunakan *private key* yang dibuat sendiri (RSA).

-----

## Langkah-Langkah Solusi

### Part 1: Persiapan dan Upload Malicious JWKS

Tujuan: Membuat pasangan kunci RSA baru dan mengunggah *public key* ke *Exploit Server* sebagai file JWKS.

1.  **Akses Lab & Setup Repeater:**

      * *Login* ke lab dengan kredensial **`wiener:peter`**.
      * Di Burp Proxy History, kirim *request* **GET /my-account** ke **Burp Repeater**.

2.  **Generate Kunci RSA Baru:**

      * Buka tab **JWT Editor** $\\rightarrow$ **Keys** di Burp Suite.
      * Klik **Generate new RSA key** dan simpan. Catat *Key ID* (`kid`) dari kunci ini.
      * Klik kanan pada kunci yang baru dibuat dan pilih **Copy Public Key as JWK**.

3.  **Konfigurasi Exploit Server:**

      * Akses **Exploit Server Editor** lab.
      * Ganti *Path File* menjadi **`/.well-known/jwks.json`**.
      * Di bagian *Body*, susun struktur **JWKS** (JSON Web Key Set) dan tempelkan *Public Key* yang sudah kamu salin ke dalam *array* `"keys"`.
      * Klik tombol **Store** dan simpan URL *Exploit Server* kamu (misalnya, `https://exploit-XXXX.web-security-academy.net/.well-known/jwks.json`).

### Part 2: Modifikasi Token dan Suntikkan JKU Header

Kita akan memodifikasi *header* dan *payload* di Burp Repeater untuk mengarahkan server ke kunci yang baru kita upload.

1.  **Akses JWT Editor:**

      * Di **Repeater**, pindah ke tab **JSON Web Token message editor**.

2.  **Modifikasi Payload (Role Escalation):**

      * Di bagian **Payload**, ubah nilai *claim* **`sub`** dari `wiener` menjadi **`administrator`**.

3.  **Modifikasi Header (Injeksi):**

      * Di bagian **Header**, lakukan perubahan berikut:
          * **`alg`**: Atur menjadi **`RS256`** (agar sesuai dengan kunci RSA).
          * **`kid`**: Ganti dengan *Key ID* (`kid`) dari kunci RSA baru yang kamu buat di Part 1.
          * **Tambahkan `jku`**: Tambahkan parameter *header* baru **`jku`** dan atur nilainya ke **URL Exploit Server** yang sudah kamu simpan.

4.  **Tanda Tangani Token yang Dimodifikasi:**

      * Klik tombol **Sign**.
      * Pilih **Private Key** dari kunci RSA yang kamu buat (kunci ini yang akan menandatangani token).
      * Klik **OK**.

### Part 3: Selesaikan Lab

1.  **Akses Admin Panel (Verifikasi):**

      * Klik **Send** pada *request* `GET /my-account`. (Respon yang berhasil adalah HTTP 200 OK dan halaman admin).

2.  **Hapus User Carlos (Final Action):**

      * Ubah *path* request dari `/my-account` menjadi:
        ```
        /admin/delete?username=carlos
        ```
      * Klik **Send**.

Lab akan ditandai **Solved\!**

-----

## Mitigasi (Pencegahan)

Serangan *JKU Header Injection* terjadi karena server memproses *public key* dari sumber eksternal yang tidak terpercaya.

1.  **Hapus atau Abaikan Header JKU/JWK:** Server harus **mengabaikan *header* `jku` atau `jwk`** jika token dikirim oleh pengguna. Server harus memuat *public key* hanya dari sumber tepercaya (konfigurasi lokal server) atau dari *JSON Web Key Set URL* yang sudah di-*whitelist* (daftar URL yang dipercaya).
2.  **Validasi Klaim `alg`:** Server harus secara ketat **memverifikasi klaim `alg`** dan hanya mengizinkan algoritma yang diharapkan (misalnya, hanya `RS256`).
3.  **Kunci Simetris (HS256):** Jika tidak ada kebutuhan untuk pihak ketiga memverifikasi token, menggunakan algoritma simetris (**HS256**) adalah cara teraman karena tidak ada *public key* yang dapat disuntikkan.

## Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-24 211807" src="https://github.com/user-attachments/assets/545f499d-d216-4441-bba1-23217e0e159c" />

<img width="1920" height="1080" alt="Screenshot 2025-09-24 212120" src="https://github.com/user-attachments/assets/4204e52a-919c-4de0-be9f-615c010037e7" />

<img width="1920" height="1080" alt="Screenshot 2025-09-24 212338" src="https://github.com/user-attachments/assets/48c98382-3683-47f4-babe-c6844132db04" />

<img width="1920" height="1080" alt="Screenshot 2025-09-24 212550" src="https://github.com/user-attachments/assets/88efcd0e-8041-4bf1-b45b-2abcf41d849a" />

<img width="722" height="800" alt="Screenshot 2025-09-24 213129" src="https://github.com/user-attachments/assets/213af58a-d44e-46b1-afa2-49f6cd5c2dfc" />
