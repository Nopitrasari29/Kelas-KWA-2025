
## Penyelesaian Tantangan: Manipulasi NoSQL

Tantangan ini mengharuskan saya untuk memperbarui semua ulasan produk secara bersamaan dengan mengeksploitasi kerentanan NoSQL Injection. Ini adalah tantangan tingkat 4 dari 6 bintang dan termasuk dalam kategori injeksi.

### Strategi dan Eksekusi Serangan

#### 1. Analisis Permintaan Normal

Langkah awal yang saya lakukan adalah menganalisis permintaan yang dikirim oleh aplikasi saat memperbarui satu ulasan produk. Saya menggunakan **Burp Suite** sebagai *interception proxy* untuk menangkap permintaan `PATCH` ke endpoint `/rest/products/reviews`. Permintaan ini berisi body JSON yang memiliki `id` spesifik ulasan dan pesan yang ingin diperbarui.

#### 2. Menyusun Payload Injeksi

Karena tantangan ini berhadapan dengan database NoSQL (MongoDB), saya menyimpulkan bahwa saya dapat memanipulasi struktur query alih-alih hanya nilainya. Tujuannya adalah memperbarui semua ulasan sekaligus.

Untuk itu, saya memilih operator MongoDB **`$ne`** (*not equal*). Dengan menggunakan `{"$ne": null}` pada field `id`, saya menciptakan sebuah kondisi yang menargetkan semua dokumen di database, karena setiap ulasan memiliki `id` yang tidak null.

#### 3. Mengirim Permintaan yang Dimodifikasi

Saya memodifikasi permintaan yang telah dicegat oleh Burp Suite. Di bagian body permintaan, saya mengganti `id` ulasan spesifik dengan payload injeksi yang sudah saya susun.

Body yang dimodifikasi menjadi:

```json
{
  "id": {"$ne": null},
  "message": "All your reviews are belong to us!"
}
```

Setelah itu, saya meneruskan permintaan yang sudah dimodifikasi ini ke server.

#### 4. Verifikasi Hasil

Setelah permintaan berhasil terkirim dan saya memuat ulang halaman produk, semua ulasan telah berubah sesuai dengan pesan yang saya suntikkan. Ini membuktikan bahwa serangan NoSQL Injection berhasil.

#### Bukti 
<img width="1854" height="1022" alt="Screenshot 2025-09-09 233013" src="https://github.com/user-attachments/assets/ccf047a4-fca0-4834-9206-559e53836b7a" />

<img width="1920" height="1080" alt="Screenshot 2025-09-09 234644" src="https://github.com/user-attachments/assets/957fc2b6-da1f-4dae-b34b-3ff60769e19c" />

<img width="1919" height="1079" alt="Screenshot 2025-09-09 234844" src="https://github.com/user-attachments/assets/1a40ad31-b689-4f60-b672-234a10e7996f" />

<img width="1865" height="365" alt="Screenshot 2025-09-09 234909" src="https://github.com/user-attachments/assets/7031c8c4-57d8-4cec-8397-89a9ea66577b" />
