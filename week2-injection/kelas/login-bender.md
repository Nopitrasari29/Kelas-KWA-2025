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
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a5c335ad-a036-47f9-8ed5-a35035e24ae2" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4ef01bcf-e4b6-48af-8258-af84e2db4d4e" />

<img width="685" height="487" alt="image" src="https://github.com/user-attachments/assets/c2aa02d3-eba6-47a4-ae86-54c8a08e798e" />

