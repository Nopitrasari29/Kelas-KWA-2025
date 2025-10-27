# Juice-Shop Write-up: Two-Factor Authentication Bypass

## Challenge Overview

**Title:** Two-Factor Authentication Bypass
**Category:** Broken Authentication / SQL Injection
**Difficulty:** ⭐⭐⭐⭐⭐

Tantangan terakhir ini menggabungkan serangan SQL Injection dan *reverse engineering* **TOTP (Time-based One-Time Password)** untuk mencuri *secret key* 2FA dari *database* dan menggunakannya untuk *login* ke akun `wurstbot`.

## Tools Used

  * **Burp Suite Repeater:** Untuk mengeksekusi dan memanipulasi *request* **GET** yang rentan.
  * **SQL Injection:** Teknik `UNION SELECT` untuk mencuri data.
  * **TOTP Generator:** Untuk menghasilkan kode 2FA yang valid.

-----

## Metodologi dan Solusi

### 1\. Eksploitasi SQL Injection dan Penentuan Kolom

  * **Aksi:** Permintaan *search* ditangkap menggunakan Burp Suite dan dikirim ke Repeater.
  * **Penentuan Kolom:** Melalui uji coba, *payload* `ORDER BY` atau `UNION SELECT` berhasil dikonfirmasi bahwa *query* asli memiliki **9 kolom** yang dapat digabungkan.

### 2\. Mencuri TOTP Secret

*Payload* SQLi dirancang untuk mencuri kolom **`totpSecret`** dari tabel `USERS` dan menampilkannya di hasil *search*.

  * **Payload yang Berhasil (9 Kolom):** *Payload* yang digunakan mencocokkan jumlah kolom dan sintaksis SQLite yang sensitif.

    ```http
    GET /rest/products/search?q='))union%20select%20id,username,email,totpSecret,5,6,7,8,9%20from%20Users--HTTP/1.1
    ```

  * **Hasil Pencurian:** Setelah *request* dikirim, *server* merespons dengan **200 OK**, dan data **`totpSecret`** berhasil diekstrak dalam *response body* di kolom yang disuntikkan: **`IFTXE3SPOEYVURT2MRYGI52TKJ4HC3KH`**.

### 3\. Bypass Two-Factor Authentication (2FA)

*Secret key* yang dicuri digunakan untuk menghasilkan kode yang diperlukan untuk *login*.

  * **Aksi:** *TOTP Secret* dimasukkan ke *online TOTP Generator* untuk menghasilkan kode 6 digit yang berubah setiap 30 detik.
  * **Penyelesaian:** Saat *prompt* 2FA muncul setelah *login* sebagai `wurstbrot`, kode 6 digit yang dihasilkan dimasukkan, berhasil mem-bypass 2FA dan menyelesaikan *challenge*.

-----

## Rekomendasi Keamanan

1.  **Parameterisasi Query:** Wajibkan penggunaan *prepared statements* untuk mencegah semua bentuk SQL Injection.
2.  **Penyimpanan Data Sensitif:** *TOTP Secret* adalah data sensitif dan harus **dienkripsi** saat disimpan di *database* serta dilindungi dari akses melalui *query* langsung.

## Bukti

<img width="720" height="387" alt="Screenshot 2025-10-27 215838" src="https://github.com/user-attachments/assets/74c8f53f-1637-4176-8df5-f50690e2f0bf" />

<img width="718" height="377" alt="Screenshot 2025-10-27 223734" src="https://github.com/user-attachments/assets/5b8ded28-f16c-408d-bf1a-0c229cc5780b" />

<img width="300" height="347" alt="Screenshot 2025-10-27 224031" src="https://github.com/user-attachments/assets/6d98a235-1bd5-42a2-812c-4ed5308cecc2" />

<img width="525" height="386" alt="Screenshot 2025-10-27 224312" src="https://github.com/user-attachments/assets/f390a0a9-0b26-4540-9187-e577437fd348" />

<img width="960" height="508" alt="Screenshot 2025-10-27 224323" src="https://github.com/user-attachments/assets/f9a05ac4-9106-478e-a539-6b008039f49d" />

