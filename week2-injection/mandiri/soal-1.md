# Write-Up: SQL Injection - Lab Retrieval of Hidden Data

## 1. Informasi Lab

*   **Sumber**: PortSwigger Web Security Academy
*   **Daftar Lab Injeksi**: [https://portswigger.net/web-security/all-labs#sql-injection](https://portswigger.net/web-security/all-labs#sql-injection)
*   **Nama Lab**: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data
*   **Vulnerability**: SQL Injection

## 2. Deskripsi Lab

Lab ini memiliki kerentanan SQL Injection pada filter kategori produk. Saat pengguna memilih kategori, aplikasi menjalankan kueri SQL di sisi server. Tujuannya adalah untuk melakukan serangan injeksi agar menampilkan produk yang seharusnya disembunyikan (produk yang belum dirilis).

## 3. Analisis Kerentanan

Kueri SQL di sisi server menggunakan klausa `WHERE` yang memfilter produk berdasarkan kategori dan status rilisnya (`released = 1`). Kueri yang rentan memiliki format sebagai berikut:

```sql
SELECT * FROM products WHERE category = '...' AND released = 1
```

Kerentanan terletak pada parameter `category` yang tidak difilter dengan benar. Hal ini memungkinkan penyerang untuk menyisipkan karakter SQL khusus untuk memanipulasi logika kueri.

## 4. Langkah-Langkah Eksploitasi

Eksploitasi dilakukan menggunakan Burp Suite untuk mencegat dan memodifikasi permintaan HTTP.

1.  **Konfigurasi Proxy**: Atur browser untuk menggunakan Burp Suite sebagai proxy agar semua lalu lintas HTTP dapat dicegat.

2.  **Intersepsi Permintaan**:
    *   Nyalakan fitur **Intercept** pada Burp Suite Proxy.
    *   Di halaman lab, klik salah satu tautan kategori produk, misalnya "Gifts".
    *   Burp Suite akan menangkap permintaan HTTP yang dikirimkan.

3.  **Injeksi Payload**:
    *   Pada Burp Suite, modifikasi baris permintaan `GET /filter?category=Gifts HTTP/2`.
    *   Ubah nilai `Gifts` dengan payload SQL Injection berikut:
        ```sql
        ' OR 1=1--
        ```
    *   Hasil akhirnya, baris request akan menjadi:
        ```http
        GET /filter?category=' OR 1=1-- HTTP/2
        ```

4.  **Penjelasan Payload**:
    *   `'` : Mengakhiri klausa string `category=`.
    *   `OR 1=1` : Menambahkan kondisi yang selalu benar ke dalam kueri. Karena klausa `OR` akan membuat seluruh kondisi `WHERE` menjadi `TRUE`, filter `AND released = 1` tidak lagi berpengaruh.
    *   `--` : Karakter komentar ini akan mengabaikan sisa dari kueri SQL, yaitu `AND released = 1`, sehingga server tidak memprosesnya.

5.  **Penyelesaian**:
    *   Kirimkan permintaan yang sudah dimodifikasi dengan menekan tombol "Forward" di Burp Suite.
    *   Halaman lab di browser akan diperbarui, menampilkan produk-produk yang sebelumnya tersembunyi.
    *   Lab ditandai sebagai "Solved", menandakan eksploitasi berhasil.

## 5. Kesimpulan

Eksploitasi SQL Injection ini berhasil dengan memanipulasi parameter `category` untuk mengubah logika kueri SQL di sisi server, memungkinkan akses ke data yang seharusnya tidak dapat diakses publik.

## Bukti 
<img width="1919" height="1062" alt="Screenshot 2025-09-10 104904" src="https://github.com/user-attachments/assets/ce0c1f1a-ec73-4ff1-bdc6-a2a86e042e9f" />

<img width="1920" height="1080" alt="Screenshot 2025-09-10 105116" src="https://github.com/user-attachments/assets/99de106a-ec78-4f6c-a766-aa1e3e77b095" />

<img width="1918" height="1079" alt="Screenshot 2025-09-10 105505" src="https://github.com/user-attachments/assets/3e388fc1-7011-4e4a-a30b-93a80c0cffe1" />

<img width="1362" height="580" alt="Screenshot 2025-09-10 105732" src="https://github.com/user-attachments/assets/ebf2b793-84c0-474d-9872-ebd97c11f2dc" />

<img width="1919" height="1079" alt="Screenshot 2025-09-10 105928" src="https://github.com/user-attachments/assets/ac8e83f9-5b49-4eb8-b44c-3b08f61d6f04" />

<img width="1919" height="1013" alt="Screenshot 2025-09-10 105915" src="https://github.com/user-attachments/assets/65a1e716-4f25-431d-8c4c-a982e78c7cd7" />




