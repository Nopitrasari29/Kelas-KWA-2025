## Juice Shop Write-up: Deprecated Interface

### Challenge Overview

**Title:** Deprecated Interface\
**Category:** Security Misconfiguration\
**Difficulty:** ⭐⭐ (2/6)

Tantangan ini bertujuan mengeksploitasi *interface* yang sudah usang dan terabaikan keamanannya, khususnya fitur *file upload* yang rentan terhadap *bypass* validasi *client-side*.

### Tools Used

-   **Web Browser**: Untuk mengakses aplikasi dan fitur keluhan (`/complain`).
-   **Burp Suite (Proxy Tool)**: Untuk menangkap (*intercept*) dan memodifikasi *request* HTTP yang dikirim ke *server*.

---

## Metodologi dan Solusi

Metode yang digunakan adalah menipu validasi *client-side* dengan ekstensi palsu, kemudian mengoreksi *request* yang tertangkap di *proxy tool* agar *server* menerima *file* XML yang seharusnya dilarang.

### 1. Identifikasi dan Persiapan Awal

1.  **Target Identifikasi:** Ditemukan bahwa laman keluhan (`/complain`) adalah *interface* lama yang mengandung *file uploader* dengan validasi yang lemah.
2.  **Analisis Validasi:** Pemeriksaan menunjukkan bahwa validasi *file type* hanya dilakukan di sisi *browser* (klien), yang dengan mudah dapat di-*bypass*.
3.  **Persiapan *Payload*:** Dibuat *file* XML sederhana (`payload.xml`). Nama *file* ini kemudian diubah menjadi **`payload.xml.zip`** agar dapat lolos *check* ekstensi awal di *browser*.

### 2. Menangkap dan Memanipulasi *Request* (Eksploitasi)

Langkah krusial ini dilakukan untuk memastikan *file* XML diterima *server* dengan nama yang benar:

1.  **Pengaturan Proxy:** Burp Suite diaktifkan untuk menangkap semua *request* dari *browser*.
2.  **Pengiriman Awal:** *File* `payload.xml.zip` dipilih di *form* keluhan, dan tombol *Submit* ditekan. *Request* **`POST` ke `/file-upload`** berhasil ditangkap oleh Burp Suite.
3.  **Modifikasi Payload:** Pada *request* yang tertangkap di Burp Suite, bagian *payload* (raw request body) diubah:
    * *Filename* dikoreksi dari `payload.xml.zip` menjadi **`payload.xml`**.
    * *Content-Type* diubah dari `application/zip` menjadi **`text/xml`**.
4.  **Forward:** *Request* yang sudah dimodifikasi diteruskan (*forward*) ke *server*.

*Server* menerima *request* ini dan berhasil memproses *file* **`payload.xml`**. Tantangan selesai karena *file* yang seharusnya ditolak berhasil diunggah dengan mengeksploitasi fitur lama yang terabaikan.

---

## Penjelasan Solusi

Tantangan ini diselesaikan dengan mengeksploitasi fitur *upload file* di *interface* yang sudah *deprecated*. Celah keamanan terjadi karena aplikasi mengandalkan validasi **client-side** yang tidak memadai. Dengan menggunakan *proxy tool* untuk memanipulasi *request* HTTP sebelum mencapai *server*, validasi tersebut berhasil di-*bypass* sepenuhnya, membuktikan adanya *Security Misconfiguration*.

## Remediasi

Untuk mencegah kebocoran keamanan dari fitur lama:

-   **Hapus atau Audit Fitur Lama:** *Interface* atau fitur yang sudah tidak digunakan harus dihapus total, bukan hanya disembunyikan. Jika dipertahankan, harus diaudit keamanannya secara berkala.
-   **Validasi Sisi Server (Wajib):** Semua validasi, terutama untuk *file upload*, harus dilakukan di sisi **server**. Validasi *client-side* hanya boleh digunakan untuk meningkatkan pengalaman pengguna, bukan sebagai lapisan keamanan.
-   **Perbarui Komponen:** Pastikan semua komponen *server* (seperti *middleware* *upload*) selalu diperbarui untuk mencegah kerentanan yang diketahui.

## Bukti
<img width="882" height="492" alt="Screenshot 2025-10-02 221810" src="https://github.com/user-attachments/assets/573232e7-6607-4bd6-b519-73aba1e8b63d" />

<img width="633" height="480" alt="Screenshot 2025-10-02 222037" src="https://github.com/user-attachments/assets/67f8bc75-c52e-4638-ab22-02caac486598" />

<img width="1453" height="779" alt="Screenshot 2025-10-02 222340" src="https://github.com/user-attachments/assets/ff7ed42a-abc3-4faf-9bae-bd1d2a804334" />

<img width="1435" height="807" alt="Screenshot 2025-10-02 222404" src="https://github.com/user-attachments/assets/a4b8ccc4-55df-4e1d-999c-5a9293be26ae" />

<img width="449" height="203" alt="Screenshot 2025-10-02 222451" src="https://github.com/user-attachments/assets/3ca4ad18-80fc-4890-a7c8-8ecacfaad96b" />
