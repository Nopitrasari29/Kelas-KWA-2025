
# Juice-Shop Write-up: Password Strength

## Challenge Overview

**Title:** Password Strength
**Category:** Broken Authentication
**Difficulty:** ⭐⭐

Tantangan ini bertujuan untuk mendapatkan akses ke akun administrator (`admin@juice-sh.op`) dengan mengeksploitasi kata sandi yang lemah melalui serangan *Brute Force*.

## Tools Used

  * **Web Browser:** Interaksi dengan aplikasi target.
  * **Burp Suite (Community Edition):** Untuk menangkap permintaan dan menjalankan serangan *Brute Force*.

## Metodologi dan Solusi

### 1\. Identifikasi Kredensial Target

  * **Aksi:** Memeriksa halaman website (ulasan produk, *source code*).
  * **Hasil:** Email administrator ditemukan: **`admin@juice-sh.op`**.

### 2\. Intercept Permintaan Login

  * **Aksi:** Atur *browser* ke **Burp Proxy** dan aktifkan **Intercept**.
  * **Aksi:** Mencoba *login* dengan email **`admin@juice-sh.op`** dan kata sandi sembarangan.
  * **Aksi:** Permintaan HTTP `POST` *login* ditangkap di Burp Proxy.
  * **Aksi:** Kirim permintaan tersebut ke modul **Intruder**.

### 3\. Konfigurasi Brute Force (Intruder)

  * **Attack Type:** Pilih **Sniper**.
  * **Positions:** Di tab **Positions**, hapus semua penanda (`§`). Tandai (**sorot**) hanya nilai *password* di *body* permintaan JSON, lalu klik **Add §**.
    > Contoh: `"password":"§1234§"`
  * **Payloads:** Pindah ke tab **Payloads**.
      * **Payload type:** Pilih **Simple list**.
      * **Input:** Masukkan kata sandi yang benar (berdasarkan hasil *brute force* di *write-up* awal) ke kotak **"Enter a new item"**, lalu klik **Add**.
        ```
        admin123
        ```

### 4\. Eksekusi dan Analisis

  * **Aksi:** Klik **Start attack**.
  * **Analisis:** Amati jendela *Attack*. Cari baris yang memiliki nilai **Length** (panjang respons) yang **sangat berbeda** dari yang lain.
  * **Penyelesaian:** Baris dengan *payload* **`admin123`** akan menunjukkan respons yang unik (misalnya, *Length* yang lebih besar atau *Status Code* 302), menandakan otentikasi berhasil dan *challenge* selesai.

## Rekomendasi Keamanan

1.  **Rate Limiting:** Batasi upaya *login* yang gagal per IP atau per akun.
2.  **Kekuatan Kata Sandi:** Wajibkan panjang minimum dan kompleksitas (kombinasi huruf, angka, simbol).
3.  **Blacklisting:** Tolak kata sandi yang umum atau sudah bocor (*e.g.*, `admin123`).

## Bukti 
<img width="805" height="509" alt="Screenshot 2025-10-27 182559" src="https://github.com/user-attachments/assets/99bf179c-fda4-4ceb-b045-922c70d09953" />

<img width="640" height="418" alt="Screenshot 2025-10-27 182814" src="https://github.com/user-attachments/assets/77b97277-e2f9-4d6c-9bfc-95ec16891170" />

<img width="597" height="251" alt="Screenshot 2025-10-27 183018" src="https://github.com/user-attachments/assets/169e4b5a-04b8-4c8e-9556-2999995281a0" />

<img width="445" height="220" alt="Screenshot 2025-10-27 183848" src="https://github.com/user-attachments/assets/dfa4e2b9-c905-43ff-ba81-5e51576a09dd" />

<img width="1920" height="1080" alt="Screenshot 2025-10-27 183903" src="https://github.com/user-attachments/assets/2911235f-cb8b-433a-9609-38a6793146e2" />

<img width="216" height="115" alt="Screenshot 2025-10-27 183933" src="https://github.com/user-attachments/assets/4e6a09ba-3de6-4f4b-be32-b0762ca8fa91" />
