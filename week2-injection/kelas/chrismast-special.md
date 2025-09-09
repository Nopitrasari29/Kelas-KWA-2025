
## Penyelesaian Tantangan: Christmas Offer

Tantangan ini memiliki tingkat kesulitan 4 dari 6 bintang dan mengharuskan saya untuk menemukan serta membeli produk "Christmas Offer" yang tersembunyi. Produk ini tidak dapat diakses melalui antarmuka web biasa, sehingga diperlukan metode yang tidak konvensional untuk menyelesaikannya.

### 1. Mengidentifikasi dan Mengambil Data Produk Tersembunyi

Produk "Christmas Offer" disembunyikan dari daftar karena statusnya di database ditandai sebagai "telah dihapus" (kolom `deletedAt` memiliki nilai). Untuk mengeksposnya, saya memanfaatkan kerentanan SQL Injection pada fungsi pencarian produk yang rentan.

*   **Titik Injeksi:** `http://127.0.0.1:3000/rest/products/search?q=`
*   **Payload:** Saya menyuntikkan payload `')) UNION SELECT * FROM Products WHERE deletedAt IS NOT NULL--` ke dalam parameter `q`. Payload ini dirancang untuk memaksa database mengembalikan data dari produk yang ditandai sebagai telah dihapus, dan mengabaikan bagian query yang tidak relevan.
*   **Hasil:** Dengan mengeksekusi payload ini di peramban, saya berhasil mendapatkan data mentah dari produk-produk yang tersembunyi dalam format JSON. Dari data ini, saya mengidentifikasi produk "Christmas Surprise Box" yang memiliki `ProductId` 10.

### 2. Menambahkan Produk ke Keranjang Melalui API

Setelah mendapatkan `ProductId` dari produk yang tersembunyi, saya tidak bisa menambahkannya ke keranjang melalui tombol "Add to basket" karena dinonaktifkan. Oleh karena itu, saya harus berinteraksi langsung dengan API backend.

#### Mengumpulkan Informasi Penting:

*   **ProductId:** Sudah diketahui, yaitu `10`.
*   **BasketId:** Saya menemukan ID keranjang belanja di `Developer Tools > Application > Session Storage`.
*   **Authorization Token:** Saya mengambil token otentikasi dari `Developer Tools > Application > Local Storage`, yang diperlukan untuk mengautentikasi permintaan API.

#### Melakukan Panggilan API:

Saya menggunakan perintah `fetch` di Console peramban untuk mengirimkan permintaan `POST` ke endpoint `/api/BasketItems/`. Permintaan ini berisi payload JSON dengan informasi yang telah dikumpulkan.

```javascript
fetch('/api/BasketItems/', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer <token_anda_di_sini>'
    },
    body: JSON.stringify({
        ProductId: 10,
        BasketId: "3", // Sesuaikan dengan nilai Anda
        quantity: 1
    })
});

**Hasil:** Permintaan berhasil dan produk "Christmas Surprise Box" muncul di keranjang belanja. Setelah itu, saya dapat melanjutkan proses checkout untuk menyelesaikan tantangan ini.
```

#### Bukti 
<img width="1830" height="727" alt="Screenshot 2025-09-09 213740" src="https://github.com/user-attachments/assets/0a028a4a-c561-4a4a-88d7-3c06958f7417" />

<img width="1845" height="734" alt="Screenshot 2025-09-09 213832" src="https://github.com/user-attachments/assets/485caa9b-fe1c-4f3f-9121-04a931c42ac7" />

<img width="1869" height="1022" alt="Screenshot 2025-09-09 222023" src="https://github.com/user-attachments/assets/103bbcb3-f456-4097-b311-587e4403a1f2" />

<img width="1835" height="960" alt="Screenshot 2025-09-09 222125" src="https://github.com/user-attachments/assets/c0dec9dc-0e23-4369-a3a7-10412853e7e1" />



