## Penyelesaian Tantangan: Login Akun Bender

### Deskripsi Misi
Tantangan ini, dengan tingkat kesulitan 3 dari 6 bintang, menugaskan saya untuk mendapatkan akses ke akun pengguna "Bender". Proses ini dilakukan dengan cara mengeksploitasi celah keamanan *SQL Injection* yang memerlukan dua tahap utama: pertama, mengidentifikasi alamat email Bender, dan kedua, menggunakan email tersebut untuk melumpuhkan mekanisme autentikasi.

### Metodologi dan Implementasi Serangan

#### Tahap Pengintaian (Information Gathering)
Serupa dengan tantangan sebelumnya yang melibatkan akun Jim, alamat email Bender tidak tersedia secara publik. Oleh karena itu, langkah awal adalah melakukan pengumpulan informasi. Terdapat dua metode yang dapat ditempuh untuk menemukan email tersebut:

1.  **Menelusuri Ulasan Produk:** Saya memeriksa bagian ulasan pada halaman produk, di mana saya berhasil menemukan sebuah ulasan yang ditinggalkan oleh pengguna "Bender" yang di dalamnya tercantum alamat emailnya.
2.  **Memeriksa Panel Administrasi:** Alternatif lain adalah dengan mengakses panel administrasi (jika sudah memiliki akses) untuk mencari data pengguna dan menemukan email yang bersangkutan.

Dari proses ini, alamat email yang berhasil ditemukan adalah `bender@juice-sh.op`.

#### Konstruksi Muatan Serangan
Setelah alamat email Bender diketahui, saya dapat merancang muatan *SQL Injection* yang bertujuan untuk mengelabui basis data agar tidak melakukan verifikasi kata sandi. Terdapat dua varian muatan yang efektif untuk tujuan ini:

1.  `bender@juice-sh.op'--`: Di sini, karakter kutip tunggal (') digunakan untuk menutup string input email, dan karakter komentar (`--`) membuat sisa kueri (termasuk bagian validasi kata sandi) diabaikan oleh basis data.
2.  `bender@juice-sh.op' OR '1'='1' --`: Muatan ini menyisipkan sebuah kondisi logis yang hasilnya selalu benar (`'1'='1'`) untuk memastikan kueri dianggap valid oleh basis data, sambil tetap menonaktifkan sisa kueri dengan tanda komentar.

#### Proses Eksekusi Serangan
1.  Pertama, saya memastikan bahwa saya telah keluar (logout) dari akun yang aktif saat ini.
2.  Pada halaman login, saya menginput salah satu dari dua muatan di atas ke dalam kolom "Email".
3.  Saya mengisi kolom "Password" dengan teks sembarang, karena bagian ini tidak akan divalidasi.
4.  Setelah menekan tombol "Log in", serangan pun berhasil dieksekusi.

### Analisis Keberhasilan
Keberhasilan serangan ini bersumber dari kegagalan aplikasi dalam melakukan proses pembersihan (sanitasi) atau validasi terhadap input yang diterima dari pengguna sebelum digabungkan ke dalam kueri SQL. Muatan yang disisipkan berhasil memanipulasi perintah yang diterima oleh basis data, sehingga proses verifikasi kata sandi dilewati secara total. Hal ini menyoroti sebuah celah keamanan fatal yang memungkinkan pengambilalihan akun pengguna mana pun, asalkan alamat email target berhasil diketahui.

### Bukti 

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a5c335ad-a036-47f9-8ed5-a35035e24ae2" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4ef01bcf-e4b6-48af-8258-af84e2db4d4e" />

<img width="685" height="487" alt="image" src="https://github.com/user-attachments/assets/c2aa02d3-eba6-47a4-ae86-54c8a08e798e" />



