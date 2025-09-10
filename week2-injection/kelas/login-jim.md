## Penyelesaian Tantangan: Akses Akun Jim

### Deskripsi Misi
Tantangan ini menugaskan saya untuk memperoleh akses ke akun pengguna bernama "Jim" dengan memanfaatkan celah keamanan *SQL Injection* yang telah ditemukan sebelumnya. Tingkat kesulitannya ditetapkan pada 3 dari 6 bintang, yang menandakan adanya rintangan tambahan, yaitu alamat email Jim yang tidak langsung tersedia.

### Metodologi dan Implementasi Serangan

#### Tahap Pengintaian (Information Gathering)
Langkah krusial pertama adalah menemukan alamat email Jim yang dirahasiakan. Untuk itu, saya melakukan penelusuran pada halaman produk dan memfokuskan pencarian pada bagian ulasan yang ditinggalkan oleh pelanggan. Melalui metode ini, saya berhasil mengidentifikasi sebuah ulasan dari pengguna "Jim" dan menemukan alamat emailnya: `jim@juice-sh.op`. Penemuan ini sekaligus mengungkap adanya kerentanan lain, yaitu *Information Disclosure* (Pembocoran Informasi) pada aplikasi.

#### Perancangan Muatan Serangan
Setelah berhasil mendapatkan alamat email target, saya menggunakan pendekatan yang identik dengan serangan sebelumnya untuk merancang muatan *SQL Injection*. Tujuan utamanya adalah untuk melumpuhkan proses verifikasi kata sandi.

#### Proses Eksekusi
1.  Saya memulai dengan memastikan sesi akun sebelumnya telah diakhiri (logout).
2.  Selanjutnya, saya navigasi kembali ke halaman login.
3.  Pada kolom isian "Email", saya menyisipkan muatan yang telah dirancang: `jim@juice-sh.op'--`.
4.  Pada kolom isian "Password", saya memasukkan serangkaian karakter acak.
5.  Setelah menekan tombol "Log in", serangan berhasil dieksekusi dan akses diberikan.

### Evaluasi Hasil
Keberhasilan serangan ini kembali disebabkan oleh kegagalan aplikasi dalam memvalidasi input dari pengguna secara semestinya, yang memungkinkan eksekusi muatan *SQL Injection*. Akibatnya, mekanisme verifikasi kata sandi dapat dilewati dengan mudah, dan saya berhasil masuk ke akun milik Jim. Prosedur serangan ini pada dasarnya sama dengan yang dilakukan pada tantangan "Login Admin," yang mengonfirmasi bahwa celah keamanan ini bersifat repetitif dan fundamental di dalam aplikasi tersebut.

### Bukti 
<img width="1919" height="1017" alt="Screenshot 2025-09-09 203744" src="https://github.com/user-attachments/assets/29d7518f-deee-4e3c-b1ba-ba83b46c9b82" />

<img width="1919" height="1033" alt="Screenshot 2025-09-09 203821" src="https://github.com/user-attachments/assets/dca56377-3085-4fd0-b776-d2b9a8cf9896" />

<img width="748" height="642" alt="Screenshot 2025-09-09 203844" src="https://github.com/user-attachments/assets/3227bbfb-48be-46f7-808c-a0574644fead" />

