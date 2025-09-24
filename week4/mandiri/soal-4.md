# Write-up: JWT Authentication Bypass via JWK Header Injection

**Link Lab:** `https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-jwk-header-injection`

## Tujuan (Goal)

Tujuan dari lab ini adalah mengeksploitasi konfigurasi *JSON Web Token* (JWT) yang lemah pada server. Server mendukung parameter **`jwk`** (*JSON Web Key*) di *header* token, dan jika salah dikonfigurasi, server akan memverifikasi tanda tangan token menggunakan *public key* yang disuntikkan oleh penyerang, yang memungkinkan penyerang menandatangani token administrator palsu menggunakan *private key* yang dibuat sendiri.

-----

## Langkah-Langkah Solusi

Lab ini diselesaikan menggunakan **Burp Suite** dengan *extension* **JWT Editor**.

### Persiapan: Akses Lab dan Tools

1.  **Akses Lab** dan *login* menggunakan kredensial yang disediakan: **`wiener:peter`**.
2.  Pastikan **Burp Proxy** aktif dan menangkap *request* yang dikirim.
3.  Di Burp Proxy **HTTP history**, temukan *request* **GET /my-account** dan kirim ke **Burp Repeater**.

### Langkah 1: Generate Kunci RSA Baru

Karena *attack* ini melibatkan injeksi *public key* dan *signing* dengan *private key*, kita memerlukan pasangan kunci **RSA (asimetris)**.

1.  Buka tab **JWT Editor** $\\rightarrow$ **Keys** di Burp Suite.
2.  Klik **Generate new RSA key** dan klik **OK** untuk menyimpan pasangan kunci baru tersebut.
    *(Kunci private akan digunakan untuk signing, dan public key akan kita injeksikan).*

### Langkah 2: Modifikasi Token dan Suntikkan JWK Header

Kita akan memodifikasi *request* `GET /my-account` di Repeater.

1.  Di **Repeater**, pindah ke tab **JSON Web Token**.
2.  **Modifikasi Payload (Role Escalation):**
      * Di bagian **Payload** (Data), ubah nilai *claim* **`sub`** dari `wiener` menjadi **`administrator`**.
3.  **Suntikkan Public Key (JWK Injection):**
      * Klik tombol **Attack** di bawah editor JWT, lalu pilih **Embedded JWK**.
      * Pilih kunci **RSA baru** yang kamu buat di Langkah 1. Klik **OK**.
      * *Periksa Header:* Parameter **`jwk`** yang berisi *public key* RSA akan ditambahkan ke *header* token.
4.  **Koreksi Algoritma:**
      * Pastikan *claim* **`alg`** di *Header* diubah menjadi **`RS256`** (agar server tahu harus memverifikasi menggunakan algoritma RSA).
5.  **Tanda Tangani Token yang Dimodifikasi:**
      * Klik tombol **Sign**.
      * Pilih **Private Key** dari kunci RSA baru yang kamu buat di Langkah 1.
      * Klik **OK**.
        *(Ini menandatangani token administrator yang baru dengan *private key* kamu, dan server akan memverifikasinya menggunakan *public key* yang kamu berikan via header `jwk`).*

### Langkah 3: Selesaikan Lab

1.  **Akses Admin Panel (Verifikasi):**
      * Klik **Send** di Repeater. *Response* yang berhasil seharusnya menunjukkan *Status code* **HTTP 200 OK** dan menampilkan halaman admin.
2.  **Hapus User Carlos (Final Action):**
      * Setelah yakin memiliki akses admin, ubah *path* pada *request* Repeater menjadi *endpoint* penghapusan user `carlos`:
        ```
        /admin/delete?username=carlos
        ```
      * Klik **Send** lagi.

Lab akan ditandai **Solved** setelah server memproses permintaan penghapusan user `carlos` menggunakan token administrator yang valid.

-----

## Mitigasi (Pencegahan)

Serangan *JWK Header Injection* terjadi karena server terlalu percaya (trust) dan secara otomatis memuat *public key* dari *header* `jwk` yang disediakan oleh pengguna, alih-alih menggunakan *public key* yang sudah tersimpan di server.

Berikut adalah cara efektif untuk memitigasi serangan ini:

1.  **Abaikan atau Nonaktifkan Header `jwk`:**

      * Cara paling aman adalah **menghapus kode yang memproses atau menerima *public key* dari *header* `jwk` atau `jku`** (*JSON Web Key Set URL*). Jika server tidak memuat kunci yang disediakan pengguna, serangan injeksi kunci menjadi mustahil.

2.  **Gunakan Algoritma Simetris (HS256) Jika Memungkinkan:**

      * Untuk sesi *internal*, gunakan algoritma simetris seperti **HS256 (HMAC with SHA-256)** yang hanya menggunakan satu kunci rahasia bersama. Ini secara inheren mencegah injeksi kunci karena tidak ada konsep *public key* yang dapat disuntikkan.

3.  **Hanya Percayai JWK dari Sumber Terpercaya (Whitelisting):**

      * Jika penggunaan kunci asimetris (RSA/RS256) tidak dapat dihindari, server harus **mengabaikan *header* `jwk`** dan **hanya memuat *public key*** dari lokasi tepercaya dan tetap (misalnya, dari konfigurasi server lokal atau *endpoint* *JSON Web Key Set* (JWKS) URL yang ketat).

4.  **Validasi Klaim `alg` (Algoritma):**

      * Server harus secara eksplisit **memeriksa klaim `alg`** di *header* token dan hanya menerima algoritma yang telah diizinkan (**whitelisted**) oleh server (misalnya, hanya `RS256`). Ini mencegah serangan *None algorithm* atau *downgrade* algoritma lainnya.

-----
## Bukti
<img width="1920" height="1080" alt="Screenshot 2025-09-24 203202" src="https://github.com/user-attachments/assets/27904917-2ae8-4679-b9d8-efdc3cb8393f" />

<img width="1920" height="1080" alt="Screenshot 2025-09-24 203658" src="https://github.com/user-attachments/assets/475b21b0-c19a-472a-a446-6a1e6a208389" />

<img width="1920" height="1080" alt="Screenshot 2025-09-24 203750" src="https://github.com/user-attachments/assets/f754c35b-5920-4f4c-8428-13a3687762ef" />

<img width="1920" height="1080" alt="Screenshot 2025-09-24 203825" src="https://github.com/user-attachments/assets/b70bdbd8-4975-43a9-a99a-22dba3bc8392" />

<img width="1920" height="1080" alt="Screenshot 2025-09-24 203919" src="https://github.com/user-attachments/assets/9ec5ce74-60bd-4b77-bc33-954622436402" />

<img width="1920" height="1080" alt="Screenshot 2025-09-24 204252" src="https://github.com/user-attachments/assets/75ffde50-68df-46f9-9a66-78803fc348a0" />

<img width="1920" height="1080" alt="Screenshot 2025-09-24 204346" src="https://github.com/user-attachments/assets/f9a7258f-929f-436f-b968-4c55090f879e" />

<img width="1920" height="1080" alt="Screenshot 2025-09-24 204400" src="https://github.com/user-attachments/assets/2d27d29b-4834-4ebb-b78d-896c83c14ae6" />
