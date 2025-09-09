## Penyelesaian Tantangan: Pengambilan Kredensial Pengguna

Tantangan ini mengharuskan saya untuk mengambil daftar lengkap kredensial pengguna, seperti email dan hash kata sandi, dari database aplikasi. Ini adalah serangan SQL Injection tingkat lanjut yang fokus pada pembocoran data sensitif dengan memanfaatkan fungsi pencarian produk.

### Strategi dan Eksekusi Serangan

#### 1. Identifikasi Titik Lemah
Saya mengidentifikasi endpoint pencarian produk sebagai titik yang rentan terhadap SQL Injection. Fitur ini berinteraksi langsung dengan database melalui parameter URL, sehingga memungkinkan untuk manipulasi query.

*   **Endpoint Target:** `http://127.0.0.1:3000/rest/products/search?q=`

#### 2. Menentukan Jumlah Kolom
Sebelum menyuntikkan query baru, saya perlu mengetahui berapa banyak kolom yang dikembalikan oleh query pencarian produk yang asli. Dengan menggunakan teknik `UNION SELECT` melalui metode *trial-and-error*, saya menemukan bahwa query asli memilih **9 kolom**.

#### 3. Menyusun Payload Injeksi
Tujuan saya adalah mendapatkan data dari tabel `Users`. Saya merancang payload `UNION SELECT` yang akan menampilkan data dari tabel `Users` dan menempelkannya ke hasil pencarian produk. Payload ini harus memiliki 9 kolom agar query berhasil.

Payload yang berhasil digunakan adalah:

```sql
x')) UNION SELECT id,email,password,role,deluxeToken,lastLoginIp,profileImage,totpSecret,isActive FROM Users--
```

**Penjelasan Payload:**
*   **`x'))`**: Digunakan untuk menutup dan mengakhiri sintaks query pencarian produk asli dengan benar.
*   **`UNION SELECT ... FROM Users`**: Menambahkan query baru yang mengambil 9 kolom data spesifik dari tabel `Users`.
*   **`--`**: Berfungsi sebagai komentar SQL, yang membuat database mengabaikan sisa query asli yang tidak diperlukan.

#### 4. Mengeksekusi Serangan
Saya menggabungkan payload yang sudah disusun ke dalam URL yang rentan. URL lengkap yang digunakan untuk mengeksploitasi celah ini, setelah di-encode, adalah:

```http
http://127.0.0.1:3000/rest/products/search?q=x'))%20UNION%20SELECT%20id,email,password,role,deluxeToken,lastLoginIp,profileImage,totpSecret,isActive%20FROM%20Users--
```
Setelah mengakses URL ini, server memproses permintaan dan mengembalikan hasil yang telah disuntikkan.

### Analisis Hasil
Serangan SQL Injection ini berhasil karena input pengguna pada fungsi pencarian tidak divalidasi atau disanitasi dengan benar. Payload `UNION SELECT` berhasil dieksekusi, sehingga data sensitif dari tabel `Users`, termasuk email dan hash kata sandi, disuntikkan ke dalam respons JSON dari hasil pencarian produk. Hal ini membuktikan bahwa kerentanan ini dapat dimanfaatkan untuk membocorkan seluruh data kredensial pengguna dari database.

### Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-10 000518" src="https://github.com/user-attachments/assets/54fdea6a-21d0-44e6-a2ff-be1099edba88" />

<img width="1920" height="1080" alt="Screenshot 2025-09-10 000641" src="https://github.com/user-attachments/assets/66e771b1-8e04-46ee-b6c9-5257bf772c03" />

<img width="1920" height="1080" alt="Screenshot 2025-09-10 000805" src="https://github.com/user-attachments/assets/76260a6e-bbda-4abf-8f8d-89b0f128e509" />

<img width="1865" height="689" alt="Screenshot 2025-09-10 000920" src="https://github.com/user-attachments/assets/11f0dac2-682d-4d1d-b606-935ac7292a67" />


