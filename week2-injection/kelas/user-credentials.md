## Penyelesaian Tantangan: User Credentials

Dalam tantangan ini, saya ditugaskan untuk mengekstrak seluruh daftar kredensial pengguna, termasuk alamat email dan hash kata sandi, langsung dari basis data aplikasi. Serangan ini merupakan implementasi *SQL Injection* tingkat lanjut yang berfokus pada eksfiltrasi (pembocoran) data sensitif melalui penyalahgunaan fungsionalitas pencarian produk.

### Metodologi dan Implementasi Serangan

#### 1. Penentuan Vektor Serangan
Vektor serangan utama diidentifikasi pada *endpoint* API yang melayani fitur pencarian produk. Fungsionalitas ini berinteraksi secara langsung dengan basis data melalui parameter URL, yang menjadikannya titik lemah yang ideal untuk manipulasi kueri.

*   **Endpoint Target:** `http://127.0.0.1:3000/rest/products/search?q=`

#### 2. Enumerasi Struktur Kueri
Sebelum dapat menyisipkan kueri baru, langkah esensial adalah mengetahui jumlah kolom yang diambil oleh kueri pencarian produk yang asli. Dengan menggunakan teknik `UNION SELECT` melalui pendekatan pengujian berulang (*trial-and-error*), saya berhasil memastikan bahwa kueri asli tersebut menyeleksi **9 kolom** data.

#### 3. Perancangan Muatan Eksfiltrasi Data
Tujuan utama saya adalah membocorkan data dari tabel `Users`. Untuk itu, saya merancang sebuah muatan `UNION SELECT` yang akan menampilkan data dari tabel `Users` dan menggabungkannya dengan hasil pencarian produk yang sah. Muatan ini harus dirancang untuk memilih 9 kolom agar struktur kueri tetap valid.

Muatan yang berhasil saya gunakan adalah sebagai berikut:
```sql
x')) UNION SELECT id,email,password,role,deluxeToken,lastLoginIp,profileImage,totpSecret,isActive FROM Users--
```
**Rincian Muatan:**
*   **`x'))`**: Bagian ini berfungsi untuk menutup sintaksis dari kueri pencarian produk yang asli secara paksa namun tetap valid.
*   **`UNION SELECT ... FROM Users`**: Perintah ini menyisipkan kueri baru yang secara spesifik mengambil 9 kolom data dari tabel `Users`.
*   **`--`**: Karakter ini berfungsi sebagai penanda komentar SQL, yang secara efektif menonaktifkan sisa kueri asli yang tidak lagi relevan.

#### 4. Implementasi Serangan
Saya kemudian menggabungkan muatan yang telah dirancang ke dalam URL target. Setelah melalui proses *URL encoding* agar dapat ditransmisikan dengan benar, URL final yang digunakan untuk melancarkan serangan adalah:
```http
http://127.0.0.1:3000/rest/products/search?q=x'))%20UNION%20SELECT%20id,email,password,role,deluxeToken,lastLoginIp,profileImage,totpSecret,isActive%20FROM%20Users--
```
Dengan mengakses URL ini, server memproses permintaan yang telah dimanipulasi dan mengembalikan data yang telah disisipkan.

### Evaluasi dan Kesimpulan
Serangan *SQL Injection* ini berhasil dieksekusi karena tidak adanya proses validasi atau sanitasi yang memadai terhadap input pengguna pada fitur pencarian. Muatan `UNION SELECT` yang saya sisipkan berhasil dieksekusi oleh server, yang mengakibatkan data sensitif dari tabel `Users`—termasuk email dan hash kata sandi—tergabung langsung ke dalam respons JSON dari hasil pencarian produk. Insiden ini membuktikan bahwa kerentanan tersebut dapat dieksploitasi untuk membocorkan seluruh data kredensial pengguna dari basis data.

### Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-10 000518" src="https://github.com/user-attachments/assets/54fdea6a-21d0-44e6-a2ff-be1099edba88" />

<img width="1920" height="1080" alt="Screenshot 2025-09-10 000641" src="https://github.com/user-attachments/assets/66e771b1-8e04-46ee-b6c9-5257bf772c03" />

<img width="1920" height="1080" alt="Screenshot 2025-09-10 000805" src="https://github.com/user-attachments/assets/76260a6e-bbda-4abf-8f8d-89b0f128e509" />

<img width="1865" height="689" alt="Screenshot 2025-09-10 000920" src="https://github.com/user-attachments/assets/11f0dac2-682d-4d1d-b606-935ac7292a67" />



