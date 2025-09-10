# Write-Up: OS Command Injection, Simple Case

## 1. Informasi Lab

*   **Sumber**: PortSwigger Web Security Academy
*   **Link Lab**: [https://portswigger.net/web-security/os-command-injection/lab-simple](https://portswigger.net/web-security/os-command-injection/lab-simple)
*   **Nama Lab**: OS command injection, simple case
*   **Vulnerability**: OS Command Injection

## 2. Deskripsi Lab

Lab ini memiliki kerentanan *OS Command Injection* pada fitur pengecekan stok produk. Tujuannya adalah untuk mengeksekusi perintah di sisi server untuk mengetahui nama pengguna yang menjalankan aplikasi, membuktikan adanya kerentanan.

## 3. Analisis Kerentanan

Aplikasi menggunakan input dari pengguna, seperti `productId` dan `storeId`, dan menyisipkannya ke dalam perintah yang dieksekusi oleh sistem operasi di server. Kerentanan ini memungkinkan penyerang untuk menambahkan perintah tambahan ke dalam input. Dengan menggunakan karakter pemisah perintah, kita bisa membuat server menjalankan perintah lain setelah perintah aslinya selesai.

## 4. Langkah-Langkah Eksploitasi

Eksploitasi dilakukan dengan mencegat dan memodifikasi permintaan `POST` dari formulir "Check stock" menggunakan Burp Suite.

1.  **Intersepsi Permintaan**:
    *   Klik "Check stock" pada produk apa pun di lab.
    *   Dengan Burp Suite Intercept menyala, permintaan `POST` akan tertangkap. Permintaan ini berisi parameter `productId` dan `storeId` di bagian *body*-nya.

2.  **Percobaan Injeksi Payload**:
    *   **Payload yang Gagal**: Percobaan awal menggunakan `&` sebagai pemisah perintah (`productId=1&storeId=1&whoami`), tetapi server tidak mengeksekusi perintah tambahan ini.
    *   **Payload yang Berhasil**: Kemudian, kami mencoba menggunakan `;` yang merupakan pemisah perintah lain di banyak sistem operasi, termasuk yang digunakan server ini. Payload yang berhasil adalah:
        ```
        productId=1;whoami
        ```
    *   Perintah ini menginstruksikan server untuk menjalankan perintah pengecekan stok, lalu secara terpisah mengeksekusi perintah `whoami`.

3.  **Penyelesaian**:
    *   Kirimkan permintaan yang sudah dimodifikasi dengan mengklik tombol "Forward" di Burp Suite.
    *   Respons dari server akan menampilkan output dari perintah `whoami`, yaitu nama pengguna. Dalam kasus ini, nama pengguna yang ditemukan adalah `peter-1v7znUv`.

## 5. Kesimpulan

Eksploitasi OS Command Injection ini berhasil karena aplikasi gagal memvalidasi input, memungkinkan eksekusi perintah yang tidak diinginkan di sisi server. Kerentanan seperti ini dapat memberikan kontrol penuh kepada penyerang atas sistem.

## Bukti 
<img width="1919" height="1020" alt="Screenshot 2025-09-10 120044" src="https://github.com/user-attachments/assets/db41c9a4-3b4a-487e-ad66-e17301dfa57f" />

<img width="1919" height="1079" alt="Screenshot 2025-09-10 120120" src="https://github.com/user-attachments/assets/dc96c77d-afca-4351-a08b-07af2a6889d5" />

<img width="1163" height="434" alt="Screenshot 2025-09-10 120308" src="https://github.com/user-attachments/assets/5e89337a-615f-4680-858f-2554398e73a2" />

<img width="1919" height="640" alt="Screenshot 2025-09-10 120328" src="https://github.com/user-attachments/assets/9c920ac9-3ffa-4975-b8aa-d58ce535efc9" />

<img width="1488" height="587" alt="Screenshot 2025-09-10 121506" src="https://github.com/user-attachments/assets/a209ef29-9969-4571-ac64-617459023695" />

<img width="1919" height="1078" alt="Screenshot 2025-09-10 121540" src="https://github.com/user-attachments/assets/012c4a86-522b-486d-9c0e-8effeb9b550f" />

<img width="1919" height="843" alt="Screenshot 2025-09-10 121550" src="https://github.com/user-attachments/assets/970278d1-efe8-49c5-9de4-45937ea79182" />




