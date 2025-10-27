# Juice-Shop Write-up: GDPR Data Erasure

## Challenge Overview

**Title:** GDPR Data Erasure
**Category:** Broken Authentication / SQL Injection
**Difficulty:** ⭐⭐⭐

Tantangan ini bertujuan untuk mendapatkan akses tidak sah ke akun pengguna yang seharusnya sudah dihapus (ditandai dengan kolom `DeletedAt` yang tidak *NULL*) melalui kerentanan SQL Injection pada formulir *login*.

## Tools Used

* **Web Browser:** Untuk interaksi dengan halaman *login*.

## Metodologi dan Solusi

### 1. Uji Kerentanan SQL Injection

Langkah ini dilakukan untuk mengkonfirmasi bahwa *input* pada kolom *Email* tidak diproses dengan aman (*vulnerable to SQLi*).

* **Aksi:** Pada halaman *Login*, masukkan *string* yang diakhiri tanda kutip tunggal (`'`).
    * **Email:** `bender@juice-sh.op'`
    * **Password:** *Password* acak.
* **Hasil:** *Server* merespons dengan anomali atau pesan *error* (seperti `[object Object]`), memvalidasi bahwa *query* SQL *server* berhasil dipecahkan oleh *input* yang tidak aman.

### 2. Crafting dan Eksekusi Payload

Berdasarkan *error* yang ditemukan (yang menyiratkan *query* asli memiliki kondisi `AND DeletedAt IS NULL`), *payload* SQLi dirancang untuk membatalkan logika *login* dan mengabaikan pengecekan *password*.

* **Tujuan Payload:** Mengubah *query* menjadi `... WHERE email = '' OR DeletedAt IS NOT NULL --`. Logika ini akan mencari akun pertama yang memiliki nilai di kolom `DeletedAt` (akun yang sudah dihapus).

* **Injeksi Payload:**
    * **Email:** **`' OR DeletedAt IS NOT NULL --`**
    * **Password:** *Password* acak (tidak relevan karena dikomentari).

### 3. Penyelesaian Challenge

* **Logika Bypass:** Bagian `--` (spasi dan dua tanda hubung) berfungsi sebagai komentar SQL, mengabaikan sisa *query* asli, termasuk pengecekan kata sandi. Kondisi `OR DeletedAt IS NOT NULL` memaksa *query* menjadi benar.
* **Hasil:** Sistem berhasil melewati otentikasi normal dan *login* ke akun yang seharusnya terhapus (akun Chris), menyelesaikan *challenge* **GDPR Data Erasure**.

## Rekomendasi Keamanan

1.  **Prepared Statements/Parameterisasi Query:** Ini adalah cara utama untuk mencegah SQL Injection. Pastikan semua *input* pengguna diperlakukan sebagai data, bukan sebagai kode yang dapat dieksekusi.
2.  **Validasi Input:** Terapkan validasi ketat pada sisi *server* untuk format email yang sah.
3.  **Error Handling:** Jangan tampilkan pesan *error* SQL mentah kepada pengguna. Informasi ini dapat memberikan petunjuk penting kepada penyerang mengenai struktur *database* aplikasi.

## Bukti 
<img width="276" height="352" alt="Screenshot 2025-10-27 191714" src="https://github.com/user-attachments/assets/f8edbeed-724c-4d39-8f42-0a6b47a464eb" />

<img width="1920" height="1080" alt="Screenshot 2025-10-27 191927" src="https://github.com/user-attachments/assets/594730eb-accc-4486-a719-5fc8634c5c15" />

<img width="217" height="113" alt="Screenshot 2025-10-27 191947" src="https://github.com/user-attachments/assets/2a77652c-c801-4de3-a7b0-01c1f332f174" />


