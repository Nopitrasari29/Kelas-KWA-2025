## Juice Shop Write-up: Error Handling Challenge

### Challenge Overview

**Title:** Error Handling\
**Category:** Security Misconfiguration\
**Difficulty:** ⭐ (1/6)

Tantangan ini mengharuskan kita untuk memprovokasi *error* pada aplikasi yang akan membocorkan detail *backend* akibat penanganan *error* yang salah (atau *verbose error*). Ini membuktikan bahwa miskonfigurasi server bisa mengungkap informasi sensitif yang bisa dieksploitasi penyerang.

### Tools Used

- Web browser (Chrome/Firefox/lainnya)
- Keyboard buat *ngetik* di URL.
- Pengetahuan tentang bagaimana *web server* merespons input yang aneh.

---

## Metodologi dan Solusi

Metodologi yang kita gunakan berfokus pada **memprovokasi *error* yang berbeda** dari *error* $404$ atau $403$ biasa, dengan mencoba *input* yang tidak valid di URL.

### 1. Eksperimen Awal dengan *Path* yang Sensitif

Awalnya, saya coba mengakses *path* umum seperti `/package.json` untuk melihat apakah ada *error* yang bocor. Meskipun itu adalah teknik yang valid, saya memutuskan untuk mencari cara lain yang bisa mengungkap detail *framework* lebih dalam.

### 2. Memprovokasi *Error* Internal Menggunakan *Encoding* URL

Saya kemudian mencoba memprovokasi *error* pada tingkat *parsing* atau *decoding* URL di sisi *server*. Teknik ini biasanya dilakukan dengan memasukkan karakter *encoding* yang tidak valid.

* **Aksi:** Saya mengubah URL utama aplikasi (misalnya: `http://localhost:3000/`) dan menambahkan karakter `%` yang berdiri sendiri di bagian akhir.
* **URL yang Dicoba:** `http://localhost:3000/%`

### 3. Analisis Hasil (*Error* Paling *Verbose*)

Saat mengakses `http://localhost:3000/%`, server merespons dengan *error* **$400$ Bad Request** yang sangat detail dan *verbose*.

* **Pesan *Error***: **$400$ URIError: Failed to decode param '%'**
* **Kebocoran Informasi ($Information Leakage$):**
    1.  **Versi Framework:** Judul *error* jelas-jelas menampilkan **"OWASP Juice Shop (Express ^4.21.0)"**. Ini langsung mengungkap teknologi *backend* dan versi spesifiknya.
    2.  ***Stack Trace***: Server menampilkan *full stack trace* JavaScript, yang menunjukkan jalur direktori internal server (misalnya: `...juice-shop/node_modules/express/lib/router/index.js`), struktur kode, dan *file* mana yang menyebabkan kegagalan (*failure*).

Informasi ini **sangat berbahaya** karena penyerang kini tahu versi Express yang digunakan dan struktur internal server. *Challenge* **Error Handling** berhasil diselesaikan!

---

## Remediasi

Untuk mencegah kebocoran informasi yang serius seperti ini di lingkungan produksi, hal-hal ini harus dilakukan:

1.  **Implementasikan Halaman *Error* Generik**: Ganti semua respons *error* $400$, $403$, $404$, dan $500$ dengan halaman *error* yang **sederhana** dan **tidak memberikan detail teknis** apa pun. Jangan pernah menampilkan *stack trace* kepada pengguna.
2.  **Konfigurasi Middleware *Error***: Pastikan *middleware* penanganan *error* (khususnya pada Express JS) dikonfigurasi untuk menyembunyikan detail sensitif saat aplikasi berjalan di mode produksi (*production mode*).
3.  **Hapus Informasi Versi**: Pastikan judul halaman atau *header* respons HTTP tidak secara eksplisit mengungkapkan versi *framework* atau *server* (seperti `Express ^4.21.0` atau `Server: Apache/2.4.1`).

## Bukti
<img width="1512" height="682" alt="Screenshot 2025-10-02 220753" src="https://github.com/user-attachments/assets/1e7d226d-d29a-4f17-a3fa-0cad7eb5572a" />

<img width="461" height="259" alt="Screenshot 2025-10-02 220601" src="https://github.com/user-attachments/assets/bb6e7554-8059-45eb-96a6-f5549b70258b" />

