Tentu, ini adalah `write-up` kedua yang Anda berikan, sudah diformat dengan benar dalam Markdown.

***

# Write-Up: SQL Injection - Lab Login Bypass

## 1. Informasi Lab

*   **Sumber**: PortSwigger Web Security Academy
*   **Link Lab**: [https://portswigger.net/web-security/sql-injection/lab-login-bypass](https://portswigger.net/web-security/sql-injection/lab-login-bypass)
*   **Nama Lab**: SQL injection vulnerability allowing login bypass
*   **Vulnerability**: SQL Injection

## 2. Deskripsi Lab

Lab ini memiliki kerentanan SQL Injection pada halaman login. Tujuannya adalah untuk melakukan serangan injeksi yang memungkinkan login sebagai pengguna `administrator` tanpa mengetahui kata sandi yang benar.

## 3. Analisis Kerentanan

Formulir login mengirimkan permintaan ke server yang kemungkinan menggunakan kueri SQL untuk memvalidasi kredensial. Struktur kueri dasarnya adalah:

```sql
SELECT * FROM users WHERE username = '...' AND password = '...'
```

Jika aplikasi tidak memfilter input dengan benar, penyerang dapat memanipulasi parameter `username` untuk mengubah logika kueri dan melewati pemeriksaan kata sandi.

## 4. Langkah-Langkah Eksploitasi

Eksploitasi dilakukan dengan mencegat dan memodifikasi permintaan `POST` dari formulir login menggunakan Burp Suite.

1.  **Akses Halaman Login**: Masuk ke halaman login lab dengan mengklik tautan "My account".

2.  **Siapkan Burp Suite**:
    *   Pastikan Burp Suite sudah berjalan dan terhubung sebagai proxy.
    *   Nyalakan fitur **Intercept** pada tab Proxy.

3.  **Cegat Permintaan POST**:
    *   Di halaman login, masukkan `username` dan `password` apa saja (misalnya, `test` dan `1234`).
    *   Klik tombol "Log in". Burp Suite akan menangkap permintaan `POST`.

4.  **Injeksi Payload**:
    *   Pada Burp Suite, modifikasi bagian **Body** dari permintaan, yang berisi parameter `username` dan `password`.
    *   Ubah nilai dari parameter `username` menjadi payload SQL Injection berikut:
        ```sql
        administrator'--
        ```    *   **Penjelasan Payload**:
        *   `administrator`: Nama pengguna yang ingin dituju.
        *   `'` : Mengakhiri klausa string `username` pada kueri SQL, memungkinkan penyisipan kode SQL.
        *   `--`: Karakter komentar yang akan membuat server mengabaikan bagian dari kueri SQL yang tersisa, yaitu pemeriksaan kata sandi (`AND password = '...'`).

5.  **Penyelesaian**:
    *   Kirimkan permintaan yang sudah dimodifikasi dengan mengklik tombol "Forward" di Burp Suite.
    *   Jika berhasil, lab akan memuat ulang dan menampilkan pesan bahwa Anda sudah berhasil masuk sebagai pengguna `administrator`.
    *   Status lab akan berubah menjadi "Solved".

## 5. Kesimpulan

Eksploitasi login bypass ini berhasil dengan menyisipkan payload SQL yang valid ke dalam parameter `username`. Hal ini mengubah logika kueri SQL di sisi server, memungkinkan login tanpa mengetahui kata sandi, yang menunjukkan kerentanan kritis pada proses autentikasi.

## Bukti 
<img width="1919" height="1079" alt="Screenshot 2025-09-10 110812" src="https://github.com/user-attachments/assets/f01687e8-17ed-48b5-b45e-7cee3b1e6c2e" />

<img width="1920" height="1080" alt="Screenshot 2025-09-10 111047" src="https://github.com/user-attachments/assets/c195f157-aadd-4368-b91b-0ece7f5f4509" />

<img width="1919" height="1079" alt="Screenshot 2025-09-10 111135" src="https://github.com/user-attachments/assets/8b26599b-20ac-4073-8d27-b4ff35ee59d7" />

<img width="1920" height="1080" alt="Screenshot 2025-09-10 111249" src="https://github.com/user-attachments/assets/88420c05-db20-48ae-8b58-0b4a3f543be9" />

<img width="1920" height="1080" alt="Screenshot 2025-09-10 111303" src="https://github.com/user-attachments/assets/390c6903-c1af-4f00-9a45-6282568e6397" />

<img width="1444" height="539" alt="Screenshot 2025-09-10 111410" src="https://github.com/user-attachments/assets/105ae0ff-afa2-4e88-8dfa-8f14a1cc2b2a" />

<img width="1919" height="1079" alt="Screenshot 2025-09-10 111451" src="https://github.com/user-attachments/assets/931c8af4-40a6-4829-a774-cbbeadf99fe8" />


