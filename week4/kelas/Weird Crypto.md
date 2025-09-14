# Juice-Shop Write-up: Weird Crypto

## Ringkasan Tantangan

**Judul:** Weird Crypto

**Kategori:** Masalah Kriptografi (Cryptographic Issues)

**Kesulitan:** ⭐⭐ (2/6)

Tantangan ini bertujuan untuk mengidentifikasi dan melaporkan penggunaan metode kriptografi yang sudah usang dalam aplikasi. Secara khusus, saya menemukan penggunaan hash MD5 yang tidak aman untuk menyimpan kata sandi.

## Alat yang Digunakan

*   **Peramban web (browser):** Untuk berinteraksi dengan aplikasi dan memeriksa token.
*   **Base64 Decoder / jwt.io:** Untuk mendekode token autentikasi.

## Metodologi dan Solusi

Metodologi yang saya gunakan untuk menyelesaikan tantangan ini adalah dengan menganalisis token autentikasi dan mencari kerentanan kriptografi di dalamnya.

1.  **Menganalisis Token Autentikasi:**
    *   Saya login ke aplikasi dan menemukan JWT (JSON Web Token) di cookie browser.
    *   Saya menyalin token tersebut dan mendekodenya. Setelah didekode, saya menemukan bahwa payload token berisi informasi pengguna, termasuk password yang di-hash.

2.  **Mengidentifikasi Kerentanan:**
    *   Saya memeriksa data yang didekode dan menemukan parameter `"password"` dengan nilai `"0192023a7bbd73250516f069df18b500"`.
    *   Dari pola dan panjangnya, saya mengidentifikasi bahwa ini adalah hash MD5, sebuah algoritma yang sudah usang dan tidak aman.

3.  **Melaporkan Masalah:**
    *   Saya menggunakan formulir kontak di aplikasi untuk melaporkan masalah ini, menjelaskan bahwa MD5 tidak cocok untuk menyimpan kata sandi karena rentan terhadap serangan.

### Penjelasan Solusi

Tantangan ini berhasil diselesaikan dengan menemukan penggunaan MD5, sebuah algoritma yang cepat dan rentan terhadap serangan *brute force* dan *collision*. Saya berhasil menyelesaikan tantangan ini dengan melaporkan kerentanan tersebut.

## Saran Perbaikan

Untuk mencegah kerentanan ini, aplikasi harus:

1.  **Tingkatkan Algoritma Hashing:** Ganti MD5 dengan algoritma yang lebih kuat seperti SHA-256 atau bcrypt.
2.  **Tambahkan Salt:** Gunakan *salt* unik untuk setiap kata sandi untuk mencegah penggunaan *rainbow table* dan meningkatkan ketahanan terhadap serangan.

## Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-14 191825" src="https://github.com/user-attachments/assets/3c16cb52-a20d-4aa0-a860-159157d71fa4" />

<img width="1920" height="1080" alt="Screenshot 2025-09-14 191917" src="https://github.com/user-attachments/assets/646409f5-5103-4275-8058-2f25a70f611c" />

<img width="1920" height="1080" alt="Screenshot 2025-09-14 192013" src="https://github.com/user-attachments/assets/2a63e36e-40d8-4e38-9835-5cdd7ef45697" />

<img width="1920" height="1080" alt="Screenshot 2025-09-14 192455" src="https://github.com/user-attachments/assets/b2b70338-154a-44bb-bfcc-dbf95010dd80" />


