# Juice-Shop Write-up: Forged Signed JWT

## Challenge Overview

**Title:** Forged Signed JWT
**Category:** Broken Authentication / Vulnerable Components
**Difficulty:** ⭐⭐⭐⭐⭐⭐ (6/6)

Tantangan level tertinggi ini mengharuskan kita untuk memalsukan token JWT yang ditandatangani menggunakan serangan **Algorithm Confusion** (Kekacauan Algoritma). Tujuannya adalah mengautentikasi sebagai pengguna fiktif **`rsa_lord@juice-sh.op`** tanpa memiliki kunci pribadi (*private key*) *server*.

## Tools Used

  * **Web Browser:** Untuk mendapatkan Kunci Publik RSA.
  * **JWT.io:** Untuk modifikasi Header/Payload dan menghasilkan tanda tangan HMAC.
  * **Burp Suite (atau *Proxy* Sejenis):** Untuk injeksi token palsu ke dalam *request* otentikasi.

## Metodologi dan Solusi

### 1\. Mengakuisisi Kunci Publik RSA

Eksploitasi ini didasarkan pada fakta bahwa *server* menggunakan Kunci Publik RSA untuk memverifikasi token yang ditandatangani dengan algoritma asimetris (RS256).

  * **Aksi:** Kunci publik (*public key*) berhasil diambil dengan mengakses *endpoint* yang terekspos di *server* Juice Shop.
  * **URL Kunci:** `http://127.0.0.1:3000/encryptionkeys/jwt.pub`
  * **Kunci yang Diperoleh:** Seluruh konten dari file **`jwt.pub`** disalin. Kunci ini akan disalahgunakan sebagai **Kunci Rahasia HMAC**.

### 2\. Mempersiapkan Token Palsu (Forging)

Token JWT yang sah (diambil dari *cookie* setelah *login*) digunakan sebagai *template* di JWT.io, kemudian dimodifikasi untuk tujuan eksploitasi.

  * **Modifikasi Payload:** Bidang `email` diubah menjadi identitas target *challenge*: **`rsa_lord@juice-sh.op`**.
  * **Modifikasi Header (Confusion Attack):** Algoritma tanda tangan diubah dari **`RS256`** (asimetris) menjadi **`HS256`** (simetris).
    ```json
    // Header yang dimodifikasi untuk confusion attack
    { "typ": "JWT", "alg": "HS256" }
    ```
    Perubahan ini mengeksploitasi *library* JWT lama yang gagal memverifikasi bahwa `alg` yang diminta adalah algoritma yang benar, sehingga memungkinkan *server* menggunakan Kunci Publik sebagai *secret* HMAC.

### 3\. Membuat Tanda Tangan HMAC Palsu

Kunci Publik RSA yang seharusnya hanya digunakan untuk verifikasi, sekarang digunakan sebagai kunci rahasia HMAC-SHA256 untuk membuat tanda tangan (Signature) yang palsu tetapi dapat diterima.

  * **Input Message:** Header dan Payload yang telah di-*encode* (`[ENCODED_HEADER].[ENCODED_PAYLOAD]`).
  * **Input Secret:** Seluruh konten **Kunci Publik RSA (`jwt.pub`)**.
  * **Algoritma:** HMAC-SHA256.
  * **Aksi:** Tanda tangan HMAC yang dihasilkan (*Hash Output*) kemudian disalin.

### 4\. Injeksi Token dan Penyelesaian Challenge

Token JWT palsu yang baru dibuat (dengan tanda tangan HMAC) diinjeksikan ke dalam *request* otentikasi.

  * **Token Final:** Token lengkap digabungkan: `[ENCODED_HEADER].[ENCODED_PAYLOAD].[HMAC_SIGNATURE_PALSU]`
  * **Aksi:** Menggunakan Burp Suite Repeater, nilai *cookie* **`token`** pada *request* (misalnya, ke `/rest/user/whoami`) diganti dengan **Token Final** yang sudah dipalsukan.

Ketika *request* dikirim, *server* Juice Shop yang rentan membaca `alg: HS256`, menggunakan Kunci Publik sebagai *secret*, memverifikasi tanda tangan HMAC yang kita buat, dan memberikan akses sebagai **`rsa_lord@juice-sh.op`**, yang berhasil menyelesaikan *challenge*.

## Rekomendasi Keamanan

1.  **Strict Algorithm Validation:** *Library* JWT harus secara tegas dikonfigurasi untuk hanya menerima algoritma yang telah ditentukan sebelumnya (misalnya, hanya `RS256`) dan mengabaikan atau menolak nilai `alg` lainnya.
2.  **Update Library:** Segera *upgrade* *library* JWT yang digunakan (*express-jwt* atau sejenisnya) ke versi terbaru yang telah menambal kerentanan *Algorithm Confusion* ini.
3.  **Kunci Publik Aman:** Pastikan Kunci Publik RSA (`jwt.pub`) tidak dapat diakses secara publik, meskipun kerentanan ini lebih berakar pada logika verifikasi daripada eksposur kunci.
