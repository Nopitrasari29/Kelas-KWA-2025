
# Juice-Shop Write-up: Unsigned JWT

## Challenge Overview

**Title:** Unsigned JWT
**Category:** Broken Authentication
**Difficulty:** ⭐⭐⭐⭐⭐ (5/6)

Tantangan ini bertujuan untuk mengeksploitasi kerentanan konfigurasi *server* yang gagal memvalidasi tanda tangan JSON Web Token (JWT) jika algoritma tanda tangan disetel ke `none`. Tujuannya adalah memalsukan token untuk mengautentikasi sebagai email tertentu: `jwtn3d@juice-sh.op`.

## Tools Used

  * **Web Browser & Developer Tools:** Untuk *login* dan mendapatkan token JWT yang sah (valid).
  * **JWT.io:** Digunakan untuk memecah (*decode*) token dan melakukan modifikasi Header/Payload.
  * **Burp Suite (atau *Proxy* Sejenis):** Untuk menangkap *request* dan memalsukan (*inject*) *cookie* token baru ke dalam *request* autentikasi ke *server*.

## Metodologi dan Solusi

### 1\. Mendapatkan dan Menganalisis Token Awal

Langkah pertama adalah mendapatkan token JWT yang sah dari aplikasi.

  * **Aksi:** Melakukan *login* ke aplikasi Juice Shop (misalnya, sebagai pengguna `bender@juice-sh.op`).
  * **Akuisisi:** Token JWT diambil dari *cookie* bernama **`token`** di *Developer Tools* *browser*.
  * **Analisis:** Token JWT yang sah dipecah di **JWT.io** untuk mendapatkan struktur Header dan Payload. Header asli (misalnya, `alg: RS256`) menunjukkan bahwa *server* mengharapkan token ditandatangani.

### 2\. Memanipulasi dan Memalsukan (Forging) JWT

Eksploitasi berfokus pada memicu kerentanan **`alg: none`** yang merupakan konfigurasi *server* yang salah, di mana *server* menerima token tanpa tanda tangan jika Header disetel demikian.

  * **Modifikasi Header:** Nilai `alg` diubah dari yang asli (`"RS256"`) menjadi **`"none"`**.
    ```json
    // Header baru (Unsigned)
    { "typ": "JWT", "alg": "none" }
    ```
  * **Modifikasi Payload:** Bidang `email` di dalam objek `data` diubah untuk memenuhi persyaratan *challenge*.
    ```json
    // Payload baru (Bagian yang diubah)
    "email": "jwtn3d@juice-sh.op"
    ```
  * **Gabungan Akhir:** Header dan Payload yang telah dimodifikasi (dan di-*encode* Base64 URL-safe) digabungkan menjadi token palsu baru dengan format **tanpa tanda tangan**:
    $$\text{[ENCODED\_HEADER]}.\text{[ENCODED\_PAYLOAD]}.$$
    (Titik terakhir dipertahankan untuk mematuhi struktur JWT.)

### 3\. Pengujian dan Eksploitasi

Token palsu diuji dengan mengganti token otentikasi pada *request* ke *server*.

  * **Aksi:** Menggunakan Burp Suite Repeater untuk mencegat *request* terautentikasi (misalnya, *whoami* atau *request* lain yang menggunakan *cookie* `token`).
  * **Injeksi:** Nilai *cookie* **`token`** diganti dengan JWT palsu yang baru dibuat.
  * **Verifikasi:** *Request* dikirimkan. *Server* yang rentan menerima token tersebut sebagai token yang valid untuk email `jwtn3d@juice-sh.op` karena pemeriksaan tanda tangan telah dilewati.

## Kesimpulan

Eksploitasi ini berhasil karena konfigurasi *server* yang memercayai instruksi penanda tanganan dari token JWT itu sendiri. Dengan mengubah algoritma tanda tangan menjadi **`none`** dan memalsukan email di Payload, otentikasi berhasil dilewati, dan *challenge* **"Unsigned JWT"** berhasil diselesaikan.

## Rekomendasi Keamanan

1.  **Tolak `alg: none`:** *Framework* atau *library* JWT harus dikonfigurasi untuk secara eksplisit menolak token yang memiliki algoritma `none`.
2.  **Verifikasi Wajib:** Selalu pastikan bahwa tanda tangan token JWT diverifikasi menggunakan kunci publik atau *secret* yang sudah diketahui *server*.
3.  **Kunci Publik Statis:** *Server* harus dikonfigurasi untuk memverifikasi hanya algoritma yang diizinkan (misalnya RS256) dan mengabaikan nilai apa pun yang dikirim oleh pengguna.

----

## Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-10-14 144831" src="https://github.com/user-attachments/assets/731e75c2-3a6e-45b7-ae02-b0eb5886dc8a" />

<img width="1920" height="1080" alt="Screenshot 2025-10-14 145108" src="https://github.com/user-attachments/assets/dfe45885-841a-469f-9513-e3a0becab374" />

<img width="495" height="399" alt="image" src="https://github.com/user-attachments/assets/8025e44b-8eb0-40bf-851c-ff0711d0be05" />
