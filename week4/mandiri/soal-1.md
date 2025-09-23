
## PortSwigger Web Security Academy: JWT Authentication Bypass via Unverified Signature

#### 1\. Penjelasan Tantangan

Tantangan ini menguji pemahaman tentang kerentanan **JWT (JSON Web Token)**, khususnya saat server gagal memverifikasi tanda tangan (`signature`) token. Tujuannya adalah untuk mendapatkan akses ke halaman `admin` dengan memanipulasi JWT dan kemudian menghapus akun `carlos`, yang menjadi syarat penyelesaian lab.

### 2\. Metodologi dan Eksploitasi

Eksploitasi ini memanfaatkan kerentanan implementasi di mana server memproses token meskipun tanda tangannya tidak valid.

-----

**Langkah 1: Menganalisis dan Mengambil JWT**

Langkah pertama adalah mendapatkan token JWT yang valid.

1.  Akses lab dan *login* dengan kredensial `wiener:peter`.
2.  Gunakan **Burp Suite** untuk mencegat permintaan. Di tab **Proxy \> HTTP history**, temukan permintaan `GET /my-account`.
3.  Di dalam *header* `Cookie` pada permintaan ini, salin seluruh nilai token `session` yang merupakan JWT.

-----

**Langkah 2: Memanipulasi JWT**

Dengan token di tangan, kita memanipulasi *payload* untuk mendapatkan akses *admin*.

1.  Salin token ke alat eksternal seperti **[jwt.io](https://jwt.io/)**.
2.  Di `jwt.io`, ubah bagian **Header** dari
    ```json
    {"alg":"RS256",...}
    ```
    menjadi
    ```json
    {"alg":"none"}
    ```
3.  Di bagian **Payload**, ubah nilai `sub` (subject/username) dari `wiener` menjadi `administrator`.
4.  Salin token baru yang dihasilkan oleh `jwt.io`. Token ini hanya memiliki **dua bagian** karena tanda tangan telah dihapus.

-----

**Langkah 3: Mengirimkan Permintaan untuk Bypass Otentikasi**

Token yang telah dimanipulasi sekarang harus ditempelkan kembali ke permintaan.

1.  Di Burp Suite, kirim permintaan `GET /my-account` ke **Repeater**.
2.  Di tab Repeater, ganti `GET /my-account` menjadi **`GET /admin`**.
3.  Pada *header* `Cookie`, ganti token JWT lama dengan token baru yang sudah dimanipulasi.
4.  Kirim permintaan tersebut. Respons yang berhasil akan memiliki status `200 OK` dan menampilkan halaman `admin`.

-----

**Langkah 4: Menyelesaikan Tantangan**

Akses ke halaman `admin` adalah bukti eksploitasi, tetapi tantangan baru akan dianggap selesai setelah akun `carlos` dihapus.

1.  Di halaman `admin` yang dimuat di *browser*, temukan dan klik tautan untuk menghapus akun `carlos`.
2.  Atau, di Burp Suite, ubah URL permintaan terakhir menjadi `GET /admin/delete?username=carlos` dan kirimkan.

Setelah permintaan ini berhasil, tantangan akan ditandai selesai.

### 3\. Mitigasi

Kerentanan ini terjadi karena implementasi server yang tidak aman. Untuk mencegahnya, hal-hal berikut harus dilakukan:

  * **Verifikasi Tanda Tangan:** Server **harus selalu memverifikasi tanda tangan** pada setiap token yang diterima.
  * **Daftar Algoritma:** Server tidak boleh menerima algoritma yang dikirim oleh klien. Sebaliknya, ia harus memiliki daftar putih (whitelist) berisi algoritma yang diizinkan. Algoritma `none` tidak boleh ada dalam daftar ini.
  * **Validasi Klien-Server:** Pastikan server tidak memercayai *payload* yang dikirim dari klien. Semua data sensitif harus divalidasi dan tidak hanya diterima begitu saja.

### Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-23 192941" src="https://github.com/user-attachments/assets/5c848cde-15a7-43f6-a1a8-4fbb48c54c3d" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 193521" src="https://github.com/user-attachments/assets/b1211620-b47b-4ef6-816f-eee8cf31af80" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 193900" src="https://github.com/user-attachments/assets/d898bae6-ec86-4ec8-89a3-f6ac4fc357a1" />

<img width="1919" height="1010" alt="Screenshot 2025-09-23 195143" src="https://github.com/user-attachments/assets/93142a24-3ea8-4565-a0aa-998d167adf26" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 195410" src="https://github.com/user-attachments/assets/c98ffe07-dcd8-4a04-b88e-5bd9c51436bd" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 195615" src="https://github.com/user-attachments/assets/c0e31b7d-0cbe-4b87-838c-05bb7a4b4a4e" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 195822" src="https://github.com/user-attachments/assets/d32d7035-c8da-4159-b05e-aafccbc20c08" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 200746" src="https://github.com/user-attachments/assets/540afbf3-1c32-4fa3-acc3-8c02507ffd4a" />

<img width="1919" height="1079" alt="Screenshot 2025-09-23 200803" src="https://github.com/user-attachments/assets/500ea5cc-4619-4a9f-b85c-a7da2a276f3d" />
