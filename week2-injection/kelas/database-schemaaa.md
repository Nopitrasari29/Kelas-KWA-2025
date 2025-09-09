## Tantangan: Database Schema

Tantangan ini mengharuskan saya untuk mengambil seluruh definisi skema database melalui eksploitasi SQL Injection. Serangan ini berbeda dari yang sebelumnya karena tujuannya bukan untuk login, melainkan untuk mengambil data internal yang sensitif.

### 1. Menemukan Kerentanan

Kerentanan utama berada pada API pencarian produk. Query di backend tidak memvalidasi input dengan benar, sehingga saya bisa menyuntikkan perintah SQL tambahan.

*   **Endpoint yang Rentan:** `http://127.0.0.1:3000/rest/products/search?q=`

### 2. Menentukan Jumlah Kolom

Sebelum melakukan serangan, saya perlu mengetahui berapa banyak kolom yang dihasilkan oleh query pencarian produk yang asli. Saya bisa menggunakan payload `UNION SELECT` dengan metode coba-coba (trial-and-error).

*   **Tujuan:** Mencoba berbagai jumlah kolom hingga server tidak mengembalikan kesalahan (error).
*   **Payload :**
  
    ```
    q=test')) UNION SELECT 1,2,3,4,5,6,7,8,9-- (Berhasil)
    ```
*   **Hasil:** Saya menemukan bahwa query asli mengembalikan 9 kolom.

### 3. Mengambil Skema Database

Setelah mengetahui jumlah kolom, saya bisa menyusun payload untuk mengambil skema database. Di database SQLite yang digunakan oleh aplikasi, informasi skema disimpan di tabel bernama `sqlite_master` atau `sqlite_schema`. Saya akan mengganti salah satu kolom dengan kolom yang berisi informasi skema.

*   **Tujuan:** Mengambil data dari tabel `sqlite_schema`, khususnya kolom `sql` yang berisi definisi skema (`CREATE TABLE...`).
*   **Payload yang Digunakan:**
    ```sql
    test')) UNION SELECT 1,2,3,4,5,6,7,8,sql FROM sqlite_schema--
    ```
*   **Penjelasan Payload:**
    *   **`test'))`**: Digunakan untuk menutup query pencarian asli.
    *   **`UNION SELECT 1,2,...,sql`**: Menggabungkan query saya dengan query asli. Saya menggunakan `sql` di posisi ke-9 untuk menampilkan definisi skema.
    *   **`FROM sqlite_schema`**: Menunjukkan dari mana data akan diambil.
    *   **`--`**: Menandai sisa query asli sebagai komentar.
*   **Eksekusi:** Saya mengakses URL dengan payload yang sudah di-encode:
    ```
    http://127.0.0.1:3000/rest/products/search?q=test'))%20UNION%20SELECT%201,2,3,4,5,6,7,8,sql%20FROM%20sqlite_schema--
    ```

### 4. Menganalisis Hasil

*   **Hasil Akhir:** Permintaan berhasil, dan server mengembalikan respons JSON yang tidak hanya berisi data produk, tetapi juga seluruh definisi skema dari tabel `sqlite_schema`.
*   **Kesimpulan:** Serangan `UNION SELECT` ini sukses karena berhasil menyuntikkan query baru ke dalam query asli, sehingga memaksa server untuk menampilkan informasi yang seharusnya tidak dapat diakses.

### 5. Bukti 
<img width="1905" height="578" alt="Screenshot 2025-09-09 211042" src="https://github.com/user-attachments/assets/005247cb-715d-4f91-a9bd-ba1551c7e8d7" />

<img width="1920" height="1080" alt="Screenshot 2025-09-09 210823" src="https://github.com/user-attachments/assets/60af30c9-46ea-41a5-b2e7-59dc46b90c39" />

<img width="1920" height="1080" alt="Screenshot 2025-09-09 210953" src="https://github.com/user-attachments/assets/480bee01-0ed1-42f4-8a63-2cef26845f23" />


<img width="1835" height="960" alt="Screenshot 2025-09-09 222125" src="https://github.com/user-attachments/assets/f10e8340-3b9c-4472-9e2b-27a46a75ed0b" />




