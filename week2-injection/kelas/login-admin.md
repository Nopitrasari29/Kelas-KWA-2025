## Penyelesaian Tantangan: Akses Administrator

### Deskripsi Misi
Tantangan ini berfokus pada upaya untuk mendapatkan akses tidak sah ke dalam akun administrator sistem. Dengan tingkat kesulitan yang tergolong rendah, yaitu 2 dari 6 bintang, serangan ini mengandalkan eksploitasi celah keamanan *SQL Injection* yang terdapat pada halaman login.

### Pendekatan dan Implementasi Serangan

#### Identifikasi Celah Keamanan
Akar dari kerentanan ini adalah praktik *SQL Injection*. Sistem pada sisi *backend* secara langsung menggabungkan masukan dari pengguna ke dalam kueri SQL tanpa melalui proses validasi atau penyaringan (sanitasi) yang memadai, sehingga membuka pintu untuk manipulasi.

#### Perancangan Muatan Eksploitasi
Tujuan utama dari serangan ini adalah untuk melompati proses verifikasi kata sandi untuk akun `admin@juice-sh.op` yang emailnya telah diketahui. Untuk itu, saya merancang sebuah muatan sederhana namun efektif: `admin@juice-sh.op'--`.

*   **Karakter Kutip Tunggal (`'`)**: Digunakan untuk mengakhiri bagian string email di dalam kueri SQL secara prematur.
*   **Karakter Komentar (`--`)**: Berfungsi sebagai penanda komentar dalam SQL, yang menyebabkan basis data mengabaikan seluruh sisa kueri, termasuk klausa yang seharusnya melakukan pengecekan kata sandi.

#### Langkah-langkah Penyerangan
1.  Saya memasukkan muatan `admin@juice-sh.op'--` ke dalam kolom isian "Email".
2.  Untuk kolom isian "Password", saya dapat memasukkan teks acak, karena bagian ini akan dinonaktifkan oleh komentar pada muatan.
3.  Setelah menekan tombol "Log in", serangan berhasil dieksekusi dan saya mendapatkan akses.

### Evaluasi Hasil
Serangan ini berhasil karena muatan yang disisipkan mampu mengubah alur logika dari kueri SQL yang dieksekusi di server. Kueri yang seharusnya memvalidasi kecocokan email dan kata sandi menjadi tidak lagi berfungsi sebagaimana mestinya. Bagian pengecekan kata sandi diabaikan sepenuhnya, sehingga sistem memberikan izin akses ke akun administrator tanpa memerlukan autentikasi yang sah.

### Bukti 
<img width="565" height="561" alt="Screenshot 2025-09-09 194123" src="https://github.com/user-attachments/assets/b4d233bf-cc82-41b5-9206-171cecdb5a3b" />

<img width="1919" height="1032" alt="Screenshot 2025-09-09 194251" src="https://github.com/user-attachments/assets/89c4e292-902f-44d5-9512-dc2180167f4f" />

<img width="791" height="857" alt="Screenshot 2025-09-09 194322" src="https://github.com/user-attachments/assets/ab42c365-677c-425a-a8c8-88a7236dd2d3" />




