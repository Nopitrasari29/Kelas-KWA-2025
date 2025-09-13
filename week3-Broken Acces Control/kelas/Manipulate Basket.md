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
<img width="1919" height="1075" alt="Screenshot 2025-09-13 140628" src="https://github.com/user-attachments/assets/472ab200-0bd8-41f7-9921-2a466adca842" />

<img width="1577" height="529" alt="Screenshot 2025-09-13 140745" src="https://github.com/user-attachments/assets/77670f31-8325-47f1-b9ba-ac080fff438c" />

<img width="1919" height="646" alt="Screenshot 2025-09-13 140822" src="https://github.com/user-attachments/assets/56d10bf3-beb2-4590-b54f-b4b88339446d" />
