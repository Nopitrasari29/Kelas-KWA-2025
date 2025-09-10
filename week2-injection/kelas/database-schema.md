## Tantangan: Database Schema

Dalam tantangan ini, saya ditugaskan untuk mengekstrak seluruh rancangan struktur (skema) dari basis data dengan memanfaatkan celah keamanan *SQL Injection*. Pendekatan serangan ini memiliki tujuan yang berbeda dari sebelumnya, yaitu untuk membocorkan data internal yang krusial, bukan untuk mendapatkan hak akses masuk.

### 1. Identifikasi Titik Lemah

Celah keamanan utamanya terletak pada *endpoint* API yang bertanggung jawab untuk fitur pencarian produk. Sistem pada sisi *backend* gagal melakukan validasi input pengguna dengan semestinya, sehingga membuka peluang untuk menyisipkan perintah SQL tambahan yang tidak sah.

*   **Endpoint Target:** `http://127.0.0.1:3000/rest/products/search?q=`

### 2. Enumerasi Kolom

Sebagai langkah awal penyerangan, sangat penting untuk mengidentifikasi jumlah kolom yang dikembalikan oleh kueri pencarian produk yang orisinal. Untuk tujuan ini, saya menggunakan perintah `UNION SELECT` dengan pendekatan pengujian berulang (*trial-and-error*) hingga menemukan jumlah yang tepat.

*   **Objektif:** Melakukan percobaan dengan jumlah kolom yang berbeda hingga respons dari server tidak lagi menunjukkan adanya kesalahan.
*   **Muatan Uji Coba:**
    ```
    q=test')) UNION SELECT 1,2,3,4,5,6,7,8,9-- (Berhasil)
    ```
*   **Kesimpulan:** Dari hasil percobaan, dapat dipastikan bahwa kueri asli menghasilkan 9 kolom data.

### 3. Ekstraksi Skema Basis Data

Dengan informasi jumlah kolom yang telah diperoleh, saya dapat merancang muatan (*payload*) spesifik untuk mengekstrak skema basis data. Pada basis data SQLite yang menjadi target, informasi mengenai skema disimpan dalam tabel sistemik bernama `sqlite_master` atau `sqlite_schema`. Saya akan memanipulasi kueri untuk mengganti salah satu kolom dengan data skema.

*   **Objektif:** Mengekstrak data dari tabel `sqlite_schema`, secara spesifik menargetkan kolom `sql` yang berisi perintah definisi tabel (`CREATE TABLE...`).
*   **Muatan Eksploitasi yang Digunakan:**
    ```sql
    test')) UNION SELECT 1,2,3,4,5,6,7,8,sql FROM sqlite_schema--
    ```
*   **Rincian Muatan:**
    *   **`test'))`**: Berfungsi untuk menutup sintaks kueri pencarian yang sah.
    *   **`UNION SELECT 1,2,...,sql`**: Menggabungkan hasil kueri orisinal dengan kueri hasil rekayasa saya. Kolom `sql` ditempatkan pada posisi ke-9 untuk menampilkan definisi skema.
    *   **`FROM sqlite_schema`**: Menargetkan tabel `sqlite_schema` sebagai sumber data.
    *   **`--`**: Mengubah sisa kueri asli menjadi komentar agar tidak dieksekusi.
*   **Implementasi Serangan:** Saya mengakses URL berikut dengan muatan yang telah di-encode agar dapat ditransmisikan dengan benar:
    ```
    http://127.0.0.1:3000/rest/products/search?q=test'))%20UNION%20SELECT%201,2,3,4,5,6,7,8,sql%20FROM%20sqlite_schema--
    ```

### 4. Hasil dan Kesimpulan

*   **Hasil Akhir:** Eksekusi muatan tersebut membuahkan hasil. Respons dari server yang diterima berformat JSON tidak lagi hanya menampilkan data produk, melainkan juga menyertakan definisi lengkap dari struktur basis data yang diambil dari tabel `sqlite_schema`.
*   **Ringkasan:** Keberhasilan serangan `UNION SELECT` ini terletak pada kemampuannya untuk menyisipkan kueri tambahan ke dalam kueri yang sudah ada, sehingga memaksa server untuk membocorkan informasi internal yang seharusnya terlindungi.

### 5. Bukti 
<img width="1905" height="578" alt="Screenshot 2025-09-09 211042" src="https://github.com/user-attachments/assets/005247cb-715d-4f91-a9bd-ba1551c7e8d7" />

<img width="1920" height="1080" alt="Screenshot 2025-09-09 210823" src="https://github.com/user-attachments/assets/60af30c9-46ea-41a5-b2e7-59dc46b90c39" />

<img width="1920" height="1080" alt="Screenshot 2025-09-09 210953" src="https://github.com/user-attachments/assets/480bee01-0ed1-42f4-8a63-2cef26845f23" />


<img width="1835" height="960" alt="Screenshot 2025-09-09 222125" src="https://github.com/user-attachments/assets/f10e8340-3b9c-4472-9e2b-27a46a75ed0b" />





