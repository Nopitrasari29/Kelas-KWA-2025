## 📝 Juice Shop Write-up: Cross-Site Imaging

### Challenge Overview

**Title:** Cross-Site Imaging  
**Category:** Cross-Site Request Forgery (CSRF) / Injection  
**Difficulty:** ⭐⭐⭐⭐⭐ (5/6)

Tujuan tantangan ini adalah memanipulasi *source URL* gambar pada halaman *Deluxe Membership* untuk menyuntikkan dan menampilkan gambar yang bersumber dari eksternal, mengeksploitasi cacat dalam penanganan *path* dan fungsi *redirect*.

### Tools Used

  - **Web Browser**: Digunakan untuk navigasi dan modifikasi parameter URL.
  - **Developer Tools**: Digunakan untuk menginspeksi kode sumber JavaScript guna memahami logika pemuatan gambar.

-----

## 💡 Metodologi dan Solusi

Metode yang diterapkan adalah analisis *frontend code* untuk mengidentifikasi parameter yang mengontrol sumber gambar, diikuti dengan konstruksi *payload* yang memanfaatkan kerentanan *relative path* dan fitur *internal redirect*.

### 1\. Analisis Kode dan Identifikasi Parameter

1.  **Akses Target:** Langkah awal adalah mengakses halaman *Deluxe Membership* untuk mengamati mekanisme pemuatan gambar.
2.  **Inspeksi JavaScript:** Melalui *Developer Tools*, kode sumber di *file* `main.js` diperiksa. Ditemukan bahwa gambar dimuat secara dinamis dan dikendalikan oleh atribut **`logoSrc`**.
3.  **Penemuan *Override***: Dikonfirmasi bahwa terdapat fungsi yang memungkinkan *path* gambar *default* ditimpa (*override*) menggunakan parameter URL bernama **`testDecal`**.

### 2\. Konstruksi *Payload* Eksploitasi

Tantangannya adalah *server* membatasi pemuatan sumber daya hanya dari *path* internal. Untuk menyuntikkan URL eksternal, *payload* harus memanfaatkan dua kelemahan secara bersamaan:

1.  ***Directory Traversal***: Digunakan urutan `../../..` untuk menavigasi keluar dari struktur direktori internal tempat gambar biasanya dimuat, mencapai *root server*.
2.  ***Internal Redirect Abuse***: *Path* kemudian diarahkan ke *endpoint* internal yang berfungsi sebagai *redirector* (misalnya `/redirect?to=...`).

### 3\. Implementasi Serangan

*Payload* akhir disusun dalam parameter `testDecal` untuk menyuntikkan *path* eksternal melalui *redirect* internal yang diizinkan.

**URL yang Disuntikkan:**

```url
http://127.0.0.1:3000/#/deluxe-membership?testDecal=../../../../redirect?to=https://dummyimage.com/600x400/fff&fx=https://github.com/bkimminich/juice-shop
```

  * **Tujuan *Payload***: String `../../..` memungkinkan manipulasi *path* yang diikuti dengan penggunaan *internal redirect* ke **`https://dummyimage.com`**.
  * **Verifikasi:** Setelah URL di-*submit*, *browser* berhasil memuat dan menampilkan gambar dari sumber eksternal yang di-*inject* tersebut, mengkonfirmasi kerentanan **Cross-Site Imaging**.

-----

## Penjelasan Solusi

Tantangan ini diselesaikan dengan menggabungkan **Directory Traversal** pada atribut `testDecal` dan penyalahgunaan fitur **Internal Redirect**. Karena *server* salah menangani *relative path* dan memiliki *internal redirect* yang tidak divalidasi dengan ketat, penyerang dapat mem-bypass batasan *same-origin* dan memaksa aplikasi memuat konten berbahaya atau konten dari situs eksternal.

## Remediasi

Untuk mencegah penyalahgunaan *redirect* dan injeksi *path*:

  * **Validasi Input Ketat:** Semua *input* pengguna yang memengaruhi *path* *file* atau URL harus divalidasi secara ketat dan disanitasi. Teknik *allowlisting* adalah yang terbaik untuk membatasi *input* ke format yang aman.
  * **Pembatasan *Redirect*:** Fungsi *redirect* internal harus menggunakan *whitelisting* ketat, membatasi tujuan *redirect* hanya pada daftar URL atau *path* internal yang telah disetujui.
  * **Gunakan *Absolute Path***: Terapkan *absolute path* untuk semua sumber daya internal, menghindari penggunaan *relative path* yang rentan terhadap manipulasi *directory traversal*.
