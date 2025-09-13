# Juice-Shop Write-up: Manipulate Basket

## Ringkasan Tantangan

**Judul:** Manipulate Basket

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Tingkat Kesulitan:** ⭐⭐⭐ (3/6)

Tantangan ini bertujuan untuk menambahkan item ke keranjang belanja pengguna lain dengan mengeksploitasi kerentanan dalam penanganan parameter permintaan.

## Alat yang Digunakan

*   **Burp Suite:** Digunakan untuk mencegat, memodifikasi, dan mengirim ulang permintaan HTTP.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan mengidentifikasi dan memanfaatkan celah dalam penanganan parameter permintaan HTTP. Aplikasi memeriksa parameter otorisasi yang pertama, tetapi menggunakan parameter duplikat yang disisipkan setelahnya untuk melakukan tindakan.

1.  **Menyiapkan dan Menangkap Permintaan:**
    *   Login ke aplikasi dan tambahkan produk ke keranjang belanja Anda.
    *   Dengan Burp Suite, permintaan `POST` yang dibuat saat menambahkan produk dicegat. Permintaan ini berisi parameter `ProductId` dan `BasketId`.

2.  **Melakukan Parameter Overloading:**
    *   Di Burp Suite, tambahkan parameter `BasketId` duplikat di bagian body permintaan.
    *   Contoh body asli:
        ```json
        {"ProductId":35,"BasketId":"1","quantity":1}
        ```
    *   Contoh body yang dimodifikasi:
        ```json
        {"ProductId":35,"BasketId":"1","BasketId":2,"quantity":1}
        ```

3.  **Verifikasi:**
    *   Setelah permintaan dimodifikasi, permintaan dikirim ke server.
    *   Item berhasil ditambahkan ke keranjang dengan ID `2` (keranjang milik pengguna lain). Tantangan ditandai sebagai `"Solved"`.

### Penjelasan Solusi

Tantangan ini berhasil dieksploitasi karena server gagal menangani parameter yang diduplikasi dengan benar. Server menggunakan `BasketId` yang pertama untuk pemeriksaan keamanan, tetapi kemudian menggunakan `BasketId` yang kedua (yang berisi ID keranjang pengguna lain) untuk melakukan operasi penambahan item. Ini menunjukkan kurangnya validasi yang ketat pada input pengguna.

## Saran Perbaikan

Untuk mencegah kerentanan ini, langkah-langkah berikut harus dipertimbangkan:

1.  **Penanganan Parameter yang Tepat:** Server harus dirancang untuk menolak atau menangani parameter duplikat dengan cara yang aman.
2.  **Kontrol Akses yang Kuat:** Pastikan semua operasi sensitif memverifikasi izin pengguna untuk melakukan tindakan pada sumber daya yang ditentukan.
3.  **Validasi di Sisi Server:** Jangan pernah mengandalkan data yang dikirimkan oleh klien untuk otorisasi. Selalu gunakan informasi dari sesi di sisi server untuk mengidentifikasi pengguna.

## Bukti 
<img width="1919" height="689" alt="Screenshot 2025-09-13 141349" src="https://github.com/user-attachments/assets/7eb6c9e7-d84c-4fbf-8d9e-60e47e2bba46" />

<img width="1919" height="1031" alt="Screenshot 2025-09-13 143336" src="https://github.com/user-attachments/assets/d331621c-ee2b-4168-8c69-ab0337ee5153" />

<img width="1919" height="736" alt="Screenshot 2025-09-13 143532" src="https://github.com/user-attachments/assets/9fc0a370-a573-47e1-9be2-506e69391c5a" />

<img width="1724" height="533" alt="Screenshot 2025-09-13 143600" src="https://github.com/user-attachments/assets/de668fee-369e-4f37-8b66-2f03e20bfa93" />
