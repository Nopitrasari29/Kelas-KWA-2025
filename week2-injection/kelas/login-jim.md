## Tantangan: Login Jim

### Deskripsi Tantangan
Tantangan ini meminta kita untuk masuk ke akun pengguna "Jim" dengan cara mengeksploitasi kerentanan SQL Injection yang sama seperti tantangan sebelumnya. Tingkat kesulitannya adalah 3 dari 6 bintang, sedikit lebih sulit karena email Jim tidak tersedia secara langsung.

### Strategi dan Eksekusi Serangan

#### Pengumpulan Informasi (Reconnaissance)
Karena email Jim tidak diketahui, langkah pertama adalah menemukannya. Saya melakukan pencarian di halaman produk dan menemukan bagian ulasan pelanggan. Dari sana, saya berhasil mengidentifikasi pengguna "Jim" yang menulis ulasan dan mendapatkan alamat emailnya: `jim@juice-sh.op`. Hal ini menunjukkan adanya kerentanan Information Disclosure di dalam aplikasi.

#### Penyusunan Payload
Setelah mendapatkan email Jim, saya menggunakan metode yang sama untuk menyusun payload SQL Injection yang bertujuan untuk melewati verifikasi kata sandi.

#### Eksekusi Serangan
1.  Saya memastikan sudah keluar dari akun sebelumnya.
2.  Saya membuka halaman login.
3.  Pada kolom Email, saya memasukkan payload `jim@juice-sh.op'--`.
4.  Saya memasukkan kata sandi acak pada kolom Password.
5.  Setelah menekan tombol "Log in," serangan berhasil dieksekusi.

### Analisis Hasil
Serangan ini berhasil karena aplikasi tidak memvalidasi input pengguna dengan benar, memungkinkan payload SQL Injection untuk dieksekusi. Hasilnya, verifikasi kata sandi dilewati, dan saya berhasil masuk ke akun Jim. Mekanisme ini identik dengan cara kerja serangan pada tantangan "Login Admin," membuktikan bahwa kerentanan ini berulang di dalam aplikasi.

### Bukti 
<img width="1919" height="1017" alt="Screenshot 2025-09-09 203744" src="https://github.com/user-attachments/assets/29d7518f-deee-4e3c-b1ba-ba83b46c9b82" />

<img width="1919" height="1033" alt="Screenshot 2025-09-09 203821" src="https://github.com/user-attachments/assets/dca56377-3085-4fd0-b776-d2b9a8cf9896" />

<img width="748" height="642" alt="Screenshot 2025-09-09 203844" src="https://github.com/user-attachments/assets/3227bbfb-48be-46f7-808c-a0574644fead" />
