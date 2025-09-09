## Tantangan: Login Admin

### Deskripsi Tantangan
Tantangan ini menargetkan akun administrator dan bertujuan untuk mendapatkan akses tidak sah ke dalamnya. Tingkat kesulitannya adalah 2 dari 6 bintang. Serangan ini berfokus pada eksploitasi kerentanan SQL Injection yang ditemukan di halaman login.

### Strategi dan Eksekusi Serangan

#### Analisis Kerentanan
Kerentanan utama adalah SQL Injection. Sistem ini rentan karena menggabungkan input pengguna secara langsung ke dalam perintah query SQL di backend, tanpa proses validasi atau sanitasi yang memadai.

#### Penyusunan Payload
Tujuan serangan adalah melewati validasi kata sandi untuk akun admin yang emailnya sudah diketahui, yaitu `admin@juice-sh.op`. Untuk mencapai hal ini, saya menyusun payload `admin@juice-sh.op'--`.

*   Tanda kutip tunggal (`'`) berfungsi untuk menutup string email dalam query.
*   Tanda `--` berfungsi sebagai komentar SQL, yang membuat database mengabaikan sisa dari perintah query, termasuk bagian yang seharusnya memeriksa kata sandi.

#### Eksekusi Serangan
1.  Saya memasukkan payload `admin@juice-sh.op'--` pada kolom Email.
2.  Pada kolom Password, saya memasukkan teks apa pun karena bagian ini akan diabaikan oleh database.
3.  Setelah menekan tombol "Log in," serangan berhasil.

### Analisis Hasil
Serangan ini berhasil karena payload yang disuntikkan secara efektif mengubah logika query SQL yang dijalankan. Query yang seharusnya memeriksa kata sandi diabaikan, sehingga memungkinkan saya masuk ke akun administrator tanpa otentikasi yang benar.

### Bukti 
<img width="565" height="561" alt="Screenshot 2025-09-09 194123" src="https://github.com/user-attachments/assets/b4d233bf-cc82-41b5-9206-171cecdb5a3b" />

<img width="1919" height="1032" alt="Screenshot 2025-09-09 194251" src="https://github.com/user-attachments/assets/89c4e292-902f-44d5-9512-dc2180167f4f" />

<img width="791" height="857" alt="Screenshot 2025-09-09 194322" src="https://github.com/user-attachments/assets/ab42c365-677c-425a-a8c8-88a7236dd2d3" />



