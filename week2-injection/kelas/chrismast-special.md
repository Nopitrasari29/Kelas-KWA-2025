## Penyelesaian Tantangan: Christmas Offer

Tantangan ini memiliki tingkat kesulitan menengah ke atas, yakni 4 dari 6 bintang, yang bertujuan untuk menemukan dan membeli sebuah produk rahasia bernama "Christmas Offer". Produk tersebut tidak ditampilkan pada antarmuka situs web standar, sehingga penyelesaiannya memerlukan pendekatan yang berbeda dari biasanya.

### Langkah 1: Eksploitasi Celah Keamanan untuk Menemukan Data Produk

Produk "Christmas Offer" sengaja disembunyikan dengan cara memberinya status "telah terhapus" pada basis data (kolom `deletedAt` diisi dengan sebuah nilai). Untuk dapat menampilkannya, saya memanfaatkan sebuah kerentanan *SQL Injection* yang terdapat pada fitur pencarian produk.

*   **URL Target Injeksi:** Celah keamanan ini ditemukan pada parameter `q` di alamat `http://127.0.0.1:3000/rest/products/search?q=`
*   **Kode Injeksi:** Saya menggunakan kode `')) UNION SELECT * FROM Products WHERE deletedAt IS NOT NULL--` yang dimasukkan ke dalam parameter `q`. Kode ini secara khusus dirancang untuk memanipulasi kueri basis data agar mengembalikan semua produk yang telah ditandai sebagai terhapus, sambil mengabaikan sisa kueri asli.
*   **Hasil Eksekusi:** Setelah menjalankan URL dengan kode injeksi tersebut pada peramban, saya berhasil memperoleh data mentah dalam format JSON yang berisi semua produk tersembunyi. Dari data tersebut, saya menemukan produk "Christmas Surprise Box" dengan `ProductId` bernilai 10.

### Langkah 2: Interaksi Langsung dengan API untuk Menambahkan Produk ke Keranjang

Meskipun `ProductId` dari produk tersembunyi telah ditemukan, tombol "Add to basket" pada halaman produk tidak aktif. Oleh karena itu, langkah selanjutnya adalah berinteraksi secara langsung dengan API (*Application Programming Interface*) pada sisi *backend*.

#### Persiapan Informasi Kunci:

*   **ProductId:** Telah didapatkan sebelumnya, yaitu `10`.
*   **BasketId:** ID keranjang belanja dapat ditemukan dengan memeriksa `Developer Tools > Application > Session Storage` pada peramban.
*   **Token Otorisasi:** Untuk proses autentikasi permintaan ke API, token otorisasi diambil dari `Developer Tools > Application > Local Storage`.

#### Eksekusi Permintaan API:

Saya memanfaatkan fitur `fetch` yang tersedia di *Console* peramban untuk mengirimkan sebuah permintaan `POST` ke *endpoint* `/api/BasketItems/`. Permintaan ini menyertakan sebuah *payload* JSON yang berisi seluruh informasi yang telah disiapkan.

```javascript
fetch('/api/BasketItems/', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer <token_anda_di_sini>'
    },
    body: JSON.stringify({
        ProductId: 10,
        BasketId: "3",
        quantity: 1
    })
});
```

**Hasil Akhir:** Permintaan API tersebut berhasil dieksekusi, yang menyebabkan produk "Christmas Surprise Box" langsung ditambahkan ke dalam keranjang belanja. Dari sini, saya dapat melanjutkan ke proses pembayaran (*checkout*) untuk menuntaskan tantangan ini.

#### Bukti 
<img width="1830" height="727" alt="Screenshot 2025-09-09 213740" src="https://github.com/user-attachments/assets/0a028a4a-c561-4a4a-88d7-3c06958f7417" />

<img width="1845" height="734" alt="Screenshot 2025-09-09 213832" src="https://github.com/user-attachments/assets/485caa9b-fe1c-4f3f-9121-04a931c42ac7" />

<img width="1869" height="1022" alt="Screenshot 2025-09-09 222023" src="https://github.com/user-attachments/assets/103bbcb3-f456-4097-b311-587e4403a1f2" />

<img width="1835" height="960" alt="Screenshot 2025-09-09 222125" src="https://github.com/user-attachments/assets/c0dec9dc-0e23-4369-a3a7-10412853e7e1" />




