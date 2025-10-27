
# Juice-Shop Write-up: Change Bender's Password

## Challenge Overview

**Title:** Change Bender's Password
**Category:** Broken Authentication
**Difficulty:** ⭐⭐⭐⭐⭐ (5/6)

Tantangan ini bertujuan untuk mengubah kata sandi akun Bender menjadi **`slurmCl4ssic`** tanpa mengetahui kata sandi aslinya. Solusi ditemukan dengan mengeksploitasi cacat *Broken Access Control* pada validasi parameter *current password* di sisi *server*.

## Tools Used

  * **Web Browser:** Untuk berinteraksi dengan halaman *login* dan *change password*.
  * **HTTP Interception Tool:** **Burp Suite**, digunakan untuk menangkap, memodifikasi, dan mengirim ulang permintaan HTTP.

## Metodologi dan Solusi

### 1\. Akses Awal ke Akun

Langkah pertama adalah mendapatkan sesi *login* yang valid sebagai Bender, yang kamu lakukan menggunakan SQL Injection.

  * **Aksi:** Melakukan *login* ke akun Bender menggunakan *payload* SQLi di kolom email dan *password* *placeholder*.
  * **Email:** `bender@juice-sh.op'` (dengan *payload* SQLi)
  * **Password *Placeholder*:** `1234`

### 2\. Intercept Permintaan Ganti Password

Setelah berhasil *login*, *request* Ganti Kata Sandi dicegat.

  * **Aksi:** Di *browser*, kamu mengisi formulir Ganti Kata Sandi dengan *password* lama sembarang dan *password* baru **`slurmCl4ssic`**.

      * **Kata Sandi Saat Ini:** `1234` (nilai *placeholder* di *query string*)
      * **Kata Sandi Baru:** `slurmCl4ssic`

  * **Intercept Hasil:** Permintaan *server* tertangkap di Burp Suite dengan metode **GET**, dan parameter *password* berada di *URL* (query string).

      * **URL yang Tertangkap:** `/rest/user/change-password?current=1234&new=slurmCl4ssic&repeat=slurmCl4ssic`

### 3\. Manipulasi Permintaan (Bypass Check)

Eksploitasi dilakukan dengan memindahkan *request* ke **Repeater** dan menghilangkan parameter `current`.

  * **Aksi:** Mengirim *request* ke **Repeater**.
  * **Aksi Kunci:** Menghapus parameter `current=1234&` dari *URL*.
  * **URL yang Dimodifikasi:**
    ```
    /rest/user/change-password?new=slurmCl4ssic&repeat=slurmCl4ssic
    ```
  * **Aksi:** Mengirim ulang *request* **GET** yang sudah dimodifikasi dari Repeater.

### 4\. Penyelesaian Challenge

  * **Hasil:** *Server* gagal memvalidasi keberadaan parameter `current` dan memproses *request* sebagai valid. *Server* merespons dengan **200 OK**, dan kata sandi Bender berhasil diubah menjadi **`slurmCl4ssic`**.

## Rekomendasi Keamanan

1.  **Validasi Wajib Parameter:** *Server* harus selalu memeriksa keberadaan dan keabsahan parameter kunci (seperti `current` password) sebelum memproses perubahan data sensitif. Jika parameter hilang, *server* harus mengembalikan kode *error* 400 atau 401, bukan melanjutkan proses.
2.  **Gunakan Metode POST:** Untuk operasi sensitif seperti perubahan *password*, selalu gunakan metode **POST** atau **PATCH** dan kirim data melalui *body* *request* (bukan *query string* GET) untuk mencegah *password* terekspos dalam log *server* atau *browser history*.

## Bukti 

<img width="722" height="442" alt="Screenshot 2025-10-27 210121" src="https://github.com/user-attachments/assets/cc2c42fa-ded4-4ef9-a146-26d196a30fa5" />

<img width="719" height="361" alt="Screenshot 2025-10-27 210351" src="https://github.com/user-attachments/assets/b8b521dd-a378-4c86-ac99-b40435512583" />
