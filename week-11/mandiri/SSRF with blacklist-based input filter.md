# PortSwigger Write-up: SSRF with blacklist-based input filter

**Lab Link:** [https://portswigger.net/web-security/ssrf/lab-ssrf-with-blacklist-filter](https://portswigger.net/web-security/ssrf/lab-ssrf-with-blacklist-filter)

## Overview

Lab ini mendemonstrasikan metode untuk melewati beberapa lapisan filter *blacklist* berbasis *input* yang dirancang untuk mencegah eksploitasi Server-Side Request Forgery (SSRF). Tujuan eksploitasi adalah mengakses antarmuka administrasi internal yang dilindungi di `http://localhost/admin` dan menghapus pengguna **`carlos`**.

-----

## Detail Kerentanan

Aplikasi ini rentan SSRF pada parameter `stockApi`, tetapi dilengkapi dengan dua filter *blacklist*:

1.  **Filter Hostname/IP:** Memblokir *hostname* umum (seperti `localhost`) dan alamat IP *loopback* standar (`127.0.0.1`).
2.  **Filter Path:** Memblokir *path* sensitif seperti `/admin`.

Eksploitasi dilakukan dengan menggunakan kombinasi **representasi IP alternatif** dan **Double URL Encoding** yang dikombinasikan dengan *Full URL Encoding* untuk seluruh *payload*.

-----

## Tools

  * **Burp Suite Professional/Community Edition** (digunakan untuk *Proxy* dan *Repeater*).
  * **Peramban Web**.

-----

## Langkah Penyelesaian

### 1\. Intercept dan Analisis Request

  * Tangkap *request* **POST** dari fitur "Check stock" menggunakan Burp Proxy dan kirim ke Repeater.
  * Identifikasi parameter yang rentan: **`stockApi`**.

### 2\. Bypass Filter Pertama (IP/Hostname)

  * Filter memblokir `127.0.0.1`. *Bypass* dilakukan dengan menggunakan representasi IP alternatif **`127.1`**.
  * Agar *request* tidak ditolak karena *encoding* yang tidak valid (berdasarkan *error* yang teramati), seluruh *payload* harus di-*encode* dengan baik.

### 3\. Bypass Filter Kedua (Path `/admin`)

  * Filter memblokir *path* yang mengandung `/admin`. *Bypass* dilakukan dengan **Double URL Encoding** pada salah satu karakter dalam string tersebut, biasanya huruf 'a'.
      * 'a' di-*encode* menjadi `%61`.
      * `%61` di-*encode* lagi menjadi **`%2561`**.

### 4\. Konstruksi Payload Final dan Eksploitasi

  * Gabungkan *bypass* IP, *bypass* path, dan perintah penghapusan pengguna, lalu lakukan *Full URL Encoding* pada seluruh nilai parameter `stockApi`:

| Elemen Target | Representasi Akhir |
| :---: | :--- |
| `http://` | `http%3A%2F%2F` |
| IP Bypass | `127.1` |
| Path Bypass | `%2561dmin` |
| Perintah Hapus | `/delete?username=carlos` |

  * **Payload Akhir yang Dieksploitasi:**

    ```
    stockApi=http%3A%2F%2F127.1%2F%2561dmin%2Fdelete%3Fusername%3Dcarlos
    ```

  * **Kirim** *request* **POST** ini menggunakan Burp Repeater. Server akan memproses *request* penghapusan tersebut secara internal, dan lab akan terpecahkan (biasanya dengan respons 302 Found dari *backend*).

-----

## Rekomendasi Solusi

Untuk memitigasi kerentanan SSRF dan filter *blacklist* yang mudah dilewati ini:

1.  **Implementasi Whitelisting (Solusi Primer):** Ganti filter *blacklist* dengan daftar putih (**Whitelisting**) yang ketat, yang secara eksplisit hanya mengizinkan *host* dan *path* eksternal yang diperlukan.
2.  **Normalisasi Input:** Pastikan semua *input* URL dinormalisasi sepenuhnya (misalnya, `127.1` diubah menjadi `127.0.0.1`) sebelum diterapkan ke filter atau sebelum *request* dikirim.
3.  **Dekode Input Sepenuhnya:** Filter harus memeriksa *input* setelah *decoding* sebanyak mungkin, bukan hanya pada satu lapis *encoding*, untuk mencegah serangan *double URL encoding*.

## Bukti 
<img width="960" height="451" alt="Screenshot 2025-11-07 222821" src="https://github.com/user-attachments/assets/c1bb6cd5-8611-4749-8672-83df4192274c" />

<img width="721" height="448" alt="Screenshot 2025-11-07 222905" src="https://github.com/user-attachments/assets/3a1ed550-3bd1-4dab-9373-5eb17fc90eec" />

<img width="723" height="412" alt="Screenshot 2025-11-07 222935" src="https://github.com/user-attachments/assets/cdcc42c3-babd-461b-a2eb-2ecfc68fe103" />

<img width="719" height="385" alt="Screenshot 2025-11-07 223129" src="https://github.com/user-attachments/assets/e4f8ff9b-32f2-4931-855b-e869f4a963da" />

<img width="960" height="512" alt="Screenshot 2025-11-07 223155" src="https://github.com/user-attachments/assets/044dd10a-bfb7-4474-b915-232ff2bf42c2" />
