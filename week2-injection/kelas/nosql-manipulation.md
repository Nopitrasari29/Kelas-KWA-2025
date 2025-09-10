## Penyelesaian Tantangan: Manipulasi NoSQL

Tantangan ini memiliki tujuan untuk memperbarui seluruh ulasan produk secara serentak dengan memanfaatkan celah keamanan *NoSQL Injection*. Misi ini digolongkan sebagai tantangan injeksi dengan tingkat kesulitan 4 dari 6 bintang.

### Metodologi dan Implementasi Serangan

#### 1. Membedah Permintaan Pembaruan Standar

Langkah pertama adalah memahami bagaimana aplikasi memproses pembaruan ulasan produk yang normal. Untuk tujuan ini, saya menggunakan perangkat lunak **Burp Suite** yang berfungsi sebagai *proxy* untuk mencegat lalu lintas jaringan. Saya berhasil menangkap sebuah permintaan `PATCH` yang dikirimkan ke *endpoint* `/rest/products/reviews`. Permintaan ini membawa sebuah *body* berformat JSON yang berisi `id` dari ulasan spesifik yang akan diubah beserta pesan pembaruannya.

#### 2. Merancang Muatan Injeksi NoSQL

Karena targetnya adalah basis data NoSQL (dalam hal ini, kemungkinan besar MongoDB), saya menyadari bahwa saya dapat memanipulasi struktur kueri itu sendiri, bukan hanya nilai datanya. Tujuannya adalah untuk membuat satu permintaan yang dapat menargetkan semua ulasan sekaligus.

Untuk mencapai ini, saya memanfaatkan operator MongoDB **`$ne`** (singkatan dari *not equal* atau "tidak sama dengan"). Dengan menyusun muatan `{"$ne": null}` pada kolom `id`, saya menciptakan sebuah kondisi yang akan cocok dengan semua dokumen di dalam koleksi ulasan, karena setiap ulasan pasti memiliki `id` yang nilainya bukan null.

#### 3. Mengirimkan Permintaan yang Telah Direkayasa

Saya kemudian mengambil permintaan yang telah saya cegat sebelumnya di Burp Suite dan melakukan modifikasi. Pada bagian *body* permintaan, saya mengganti nilai `id` ulasan yang tadinya spesifik dengan muatan injeksi yang telah saya siapkan.

Struktur *body* yang telah dimodifikasi menjadi seperti berikut:

```json
{
  "id": {"$ne": null},
  "message": "All your reviews are belong to us!"
}
```

Setelah modifikasi selesai, saya meneruskan permintaan yang telah direkayasa ini ke server untuk dieksekusi.

#### 4. Konfirmasi Keberhasilan

Untuk memverifikasi hasil serangan, saya memuat ulang halaman produk pada peramban. Hasilnya, semua ulasan yang ada telah berubah menjadi pesan yang saya sisipkan melalui muatan. Hal ini menjadi bukti konkret bahwa serangan *NoSQL Injection* telah berhasil dijalankan dengan sukses.

#### Bukti 
<img width="1854" height="1022" alt="Screenshot 2025-09-09 233013" src="https://github.com/user-attachments/assets/ccf047a4-fca0-4834-9206-559e53836b7a" />

<img width="1920" height="1080" alt="Screenshot 2025-09-09 234644" src="https://github.com/user-attachments/assets/957fc2b6-da1f-4dae-b34b-3ff60769e19c" />

<img width="1919" height="1079" alt="Screenshot 2025-09-09 234844" src="https://github.com/user-attachments/assets/1a40ad31-b689-4f60-b672-234a10e7996f" />

<img width="1865" height="365" alt="Screenshot 2025-09-09 234909" src="https://github.com/user-attachments/assets/7031c8c4-57d8-4cec-8397-89a9ea66577b" />

