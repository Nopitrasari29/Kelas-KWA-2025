## Tantangan: Login Bender

### Deskripsi Tantangan
Tantangan ini memiliki tingkat kesulitan 3 dari 6 bintang dan bertujuan untuk masuk ke akun pengguna "Bender" dengan mengeksploitasi kerentanan SQL Injection. Serangan ini memerlukan identifikasi email Bender terlebih dahulu, lalu mem-bypass mekanisme otentikasi.

### Strategi dan Eksekusi Serangan

#### Pengumpulan Informasi (Reconnaissance)
Sama seperti tantangan Login Jim, email Bender tidak tersedia di awal. Ada dua cara untuk menemukannya:

1.  **Mencari di Ulasan Produk:** Saya menjelajahi ulasan-ulasan produk dan menemukan ulasan dari pengguna "Bender" yang menyertakan emailnya.
2.  **Mencari di Panel Admin:** Saya juga bisa mencari emailnya dari panel administrasi, di mana data pengguna tersimpan.

Email yang ditemukan adalah `bender@juice-sh.op`.

#### Penyusunan Payload
Dengan email Bender yang sudah diketahui, saya menyusun payload SQL Injection untuk menipu database agar tidak memeriksa kata sandi. Ada dua jenis payload yang dapat digunakan:

1.  `bender@juice-sh.op'--`: Tanda kutip tunggal (') menutup string email, dan `--` mengomentari sisa query, termasuk bagian verifikasi kata sandi.
2.  `bender@juice-sh.op' OR '1'='1' --`: Payload ini menambahkan kondisi yang selalu bernilai benar (`'1'='1'`) untuk memastikan query berhasil, sambil tetap mengomentari sisa query.

#### Eksekusi Serangan
1.  Saya memastikan sudah keluar dari akun sebelumnya.
2.  Pada halaman login, saya memasukkan salah satu payload di atas pada kolom Email.
3.  Pada kolom Password, saya memasukkan teks acak, karena kata sandi tidak akan diperiksa.
4.  Setelah menekan tombol "Log in," serangan berhasil.

### Analisis Hasil
Serangan ini berhasil karena aplikasi gagal membersihkan atau memvalidasi input pengguna sebelum menggabungkannya ke dalam query SQL. Payload yang dimasukkan mengubah perintah database sehingga verifikasi kata sandi dilewati sepenuhnya. Hal ini menunjukkan kerentanan kritis yang memungkinkan pengambilalihan akun mana pun hanya dengan mengetahui emailnya.

### Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-09 210823" src="https://github.com/user-attachments/assets/b47d8706-f9ea-4bf4-83f6-ca9d2e01307b" />

<img width="1920" height="1080" alt="Screenshot 2025-09-09 210953" src="https://github.com/user-attachments/assets/f5812611-33c7-4ab8-984a-3324368dabe4" />

<img width="1275" height="282" alt="Screenshot 2025-09-09 211025" src="https://github.com/user-attachments/assets/cf5018f8-763e-47bb-90ce-c9423aed7575" />

<img width="1905" height="578" alt="Screenshot 2025-09-09 211042" src="https://github.com/user-attachments/assets/d32db4fd-aae1-48bc-b8a5-67d4ddce2289" />
