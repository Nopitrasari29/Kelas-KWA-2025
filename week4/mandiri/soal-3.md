## PortSwigger Lab: Modifying serialized objects

**Link Lab:** `https://portswigger.net/web-security/deserialization/exploiting/lab-deserialization-modifying-serialized-objects`

### 1. Ringkasan Tantangan

Tantangan ini adalah tentang **insecure deserialization**. Tujuan utamanya adalah untuk mendapatkan hak akses administrator dengan memanipulasi objek sesi yang disimpan di dalam *cookie* browser. Setelah hak akses berhasil dinaikkan, tugas terakhir adalah menghapus akun `carlos`, yang menjadi syarat penyelesaian lab.

---
### 2. Metodologi dan Eksploitasi

Eksploitasi ini memanfaatkan kerentanan pada cara server memproses data ter-serial yang dikirim dari klien.

**Langkah 1: Mengambil Objek Ter-serial**

Langkah pertama adalah mendapatkan data objek yang dikirim oleh server ke klien. Saya *login* ke lab dengan kredensial `wiener:peter` dan menggunakan **Burp Suite** untuk mencegat permintaan `GET /my-account`. Di dalam *header* `Cookie` permintaan ini, saya menemukan nilai sesi yang merupakan objek yang diserialisasi dan di-*encode* dalam format **Base64**.

**Langkah 2: Mendekode dan Memodifikasi Objek**

Saya menyalin *string* Base64 dari *cookie* dan menggunakan alat eksternal atau **Decoder Burp** untuk mendekodenya. Hasilnya adalah objek yang dapat dibaca manusia, dalam format serialisasi PHP.
* **Objek Asli:** `O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:0;}`
Di sini, `b:0;` menunjukkan nilai `false` untuk properti `admin`. Saya mengubahnya menjadi **`b:1;`** untuk membuatnya `true`.

**Langkah 3: Menyandi Ulang dan Mengirim Permintaan**

Setelah memodifikasi objek, saya menyalinnya dan menyandikannya kembali ke format **Base64** yang benar.
* **Objek Termodifikasi:** `O:4:"User":2:{s:8:"username";s:6:"wiener";s:5:"admin";b:1;}`
* **Hasil Encode (Base64):** `Tzo0OiJVc2VyIjoyOntzOjg6InVzZXJuYW1lIjtzOjY6IndpZW5lciI7czo4OiJpc0FkbWluIjtiOjE7fQ==`
Di **Burp Repeater**, saya mengganti *cookie* lama dengan yang baru. Kemudian, saya mengubah permintaan dari `GET /my-account` menjadi **`GET /admin`** dan mengirimkannya. Server memproses objek yang dimodifikasi, dan memberikan akses ke halaman admin.

**Langkah 4: Menyelesaikan Lab**

Setelah berhasil mendapatkan akses ke halaman `admin`, tugas terakhir adalah menghapus akun `carlos` untuk menyelesaikan tantangan. Saya menemukan tautan untuk menghapus akun di halaman admin dan mengirimkan permintaan ke URL `.../admin/delete?username=carlos`. Setelah permintaan ini berhasil, lab ditandai selesai.

---
### 3. Mitigasi

Kerentanan ini bisa dicegah dengan langkah-langkah berikut:
* **Jangan Percayai Data yang Datang dari Klien:** Aplikasi tidak seharusnya mendeserialisasi data dari sumber yang tidak terpercaya.
* **Gunakan Format yang Aman:** Lebih baik menggunakan format data yang aman seperti **JSON** daripada serialisasi yang rentan terhadap *object injection*.
* **Verifikasi Integritas Data:** Jika serialisasi harus digunakan, pastikan untuk menggunakan **tanda tangan digital** untuk memverifikasi bahwa objek tidak diubah di sisi klien.

### Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-23 204116" src="https://github.com/user-attachments/assets/10fd732e-bf75-4c4a-b98e-c1594cbe0576" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 205207" src="https://github.com/user-attachments/assets/38cccc9f-e972-4b73-bcf5-f426896f5945" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 205723" src="https://github.com/user-attachments/assets/6a988089-360b-43db-a11d-d0c6da136d49" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 210100" src="https://github.com/user-attachments/assets/ea57b4dc-6525-4790-b334-709bbc93c005" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 210842" src="https://github.com/user-attachments/assets/ef74bc68-294b-49a3-943e-4f7d28c9951b" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 210914" src="https://github.com/user-attachments/assets/3200f989-83a9-49d6-aae1-77685a9ebbda" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 210927" src="https://github.com/user-attachments/assets/b31b1360-d3d7-4aa5-8319-16372a55d821" />
