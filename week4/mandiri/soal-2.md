## **PortSwigger Lab: JWT authentication bypass via flawed signature verification**

**Link Lab:** `https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-flawed-signature-verification`

#### **1. Penjelasan Tantangan**

Tantangan ini menguji kerentanan pada implementasi **JSON Web Token (JWT)** di mana server gagal memverifikasi tanda tangan kriptografis dari token sesi. Tujuannya adalah untuk memanipulasi token JWT untuk mendapatkan hak akses sebagai `administrator`, yang kemudian digunakan untuk menghapus akun `carlos`, dan menyelesaikan lab.

---

### **2. Metodologi dan Eksploitasi**

Eksploitasi ini didasarkan pada celah kepercayaan: server menganggap token yang datang dari klien sudah diverifikasi, padahal tidak.

**Langkah 1: Mengambil dan Menganalisis JWT**

Pertama, saya mengautentikasi diri ke lab dengan kredensial `wiener:peter`. Menggunakan **Burp Suite**, saya menangkap permintaan `GET /my-account` yang dikirim setelah proses *login* berhasil. Permintaan ini berisi *cookie* sesi, yang nilainya adalah token JWT.

**Langkah 2: Memanipulasi Token JWT**

Saya menganalisis token JWT yang diperoleh. Token JWT terdiri dari tiga bagian: Header, Payload, dan Signature.

* **Header:** Menentukan algoritma tanda tangan.
* **Payload:** Berisi klaim data, seperti nama pengguna (`sub`).
* **Signature:** Tanda tangan kriptografis untuk memverifikasi integritas token.

Saya menggunakan alat seperti [jwt.io](https://jwt.io/) untuk memanipulasi token. Saya membuat dua perubahan penting:

1.  **Mengubah Algoritma:** Saya mengubah nilai `alg` di Header dari `RS256` (algoritma yang aman) menjadi **`none`**. Ini secara eksplisit memberi tahu server untuk tidak melakukan verifikasi tanda tangan.
2.  **Mengubah Hak Akses:** Saya mengubah nilai `sub` di Payload dari `wiener` menjadi **`administrator`**.

Setelah perubahan ini, saya menghapus Signature dan menyalin token baru yang hanya memiliki dua bagian.

**Langkah 3: Mengirimkan Permintaan Bypass**

Dengan token yang dimanipulasi, saya kembali ke **Burp Suite** dan mengirim permintaan `GET` ke jalur `/admin`, mengganti token di *header* `Cookie` dengan token yang baru. Server yang rentan menerima token ini, mengabaikan Signature yang hilang, dan memberikan akses `administrator` karena klaim di Payload yang dimanipulasi.

**Langkah 4: Menyelesaikan Lab**

Setelah mendapatkan akses `administrator`, tugas terakhir adalah menghapus akun `carlos`. Saya menemukan URL yang dibutuhkan di halaman `admin` dan mengirimkan permintaan `GET` ke sana.

URL yang digunakan adalah: `https://[lab-id].web-security-academy.net/admin/delete?username=carlos`.

Mengirimkan permintaan ini berhasil menghapus akun `carlos` dan menyelesaikan tantangan.

---

### **3. Mitigasi**

Kerentanan ini sepenuhnya dapat dicegah dengan praktik keamanan yang baik:

1.  **Verifikasi Tanda Tangan:** Server harus **selalu memvalidasi tanda tangan** pada setiap token yang diterima. Jika tanda tangan tidak ada atau tidak valid, token harus ditolak.
2.  **Daftar Putih Algoritma:** Server tidak boleh memercayai algoritma yang dikirim oleh klien. Sebaliknya, server harus menggunakan algoritma yang telah ditentukan sebelumnya. Mengizinkan algoritma `none` merupakan kesalahan fatal dalam implementasi.
3.  **Kerahasiaan Kunci:** Kunci rahasia untuk tanda tangan (seperti kunci privat dalam skema RSA) harus disimpan dengan aman dan tidak boleh bocor.

### Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-23 202304" src="https://github.com/user-attachments/assets/0e17d931-492f-4728-be60-f44afc404e7a" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 202559" src="https://github.com/user-attachments/assets/6b185ff0-0e28-417e-947a-f903ddda3c18" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 202651" src="https://github.com/user-attachments/assets/52f62a3f-ddd6-4027-b4d0-dbd6a64b6f66" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 202712" src="https://github.com/user-attachments/assets/b9c0606d-9898-41b4-85cb-a30255c3a888" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 202806" src="https://github.com/user-attachments/assets/3b806c3f-b90b-4c99-bafe-13b53087f028" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 202932" src="https://github.com/user-attachments/assets/b55f6e55-d2be-47bf-bb8a-6b61f6760d21" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 203054" src="https://github.com/user-attachments/assets/45b8b490-ee73-412e-83d3-2c072a5602dc" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 203117" src="https://github.com/user-attachments/assets/71c7392f-be98-413f-a523-9a439504d026" />
