# Write-Up: XXE Injection - Lab Retrieve Files

## 1. Informasi Lab

*   **Sumber**: PortSwigger Web Security Academy
*   **Link Lab**: [https://portswigger.net/web-security/xxe/lab-exploiting-xxe-to-retrieve-files](https://portswigger.net/web-security/xxe/lab-exploiting-xxe-to-retrieve-files)
*   **Nama Lab**: Exploiting XXE using external entities to retrieve files
*   **Vulnerability**: XML External Entity (XXE) Injection

## 2. Deskripsi Lab

Lab ini memiliki kerentanan *XML External Entity (XXE) Injection* pada fitur "Check stock" di sebuah toko online. Tujuannya adalah untuk melakukan serangan injeksi yang memaksa server untuk menampilkan isi dari file lokal, yaitu `/etc/passwd`.

## 3. Analisis Kerentanan

Aplikasi memproses input data dari pengguna dalam format XML. Kerentanan muncul ketika parser XML di sisi server dikonfigurasi untuk mengizinkan entitas eksternal. Hal ini memungkinkan penyerang untuk mendefinisikan entitas yang mengacu pada sebuah file di sistem server, kemudian memanggil entitas tersebut agar konten file ditampilkan dalam respons.

## 4. Langkah-Langkah Eksploitasi

Eksploitasi dilakukan dengan mencegat dan memodifikasi permintaan `POST` dari fitur "Check stock" menggunakan Burp Suite.

1.  **Akses Lab dan Intersepsi**:
    *   Di halaman produk, klik tombol "Check stock".
    *   Dengan Burp Suite Intercept menyala, permintaan `POST` akan tertangkap. Permintaan ini berisi data XML di bagian *body*-nya.

2.  **Suntikkan Payload XML**:
    *   Modifikasi data XML di Burp Suite dengan menyisipkan deklarasi `DOCTYPE` dan `ENTITY` di antara deklarasi XML dan elemen root `<stockCheck>`.
    *   **Payload yang disuntikkan**:
        ```xml
        <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
        ```

3.  **Panggil Entitas**:
    *   Setelah entitas `xxe` didefinisikan, panggil entitas tersebut di dalam elemen `<productId>` dengan mengubah nilainya menjadi `&xxe;`.

4.  **Payload XML Lengkap**:
    *   Berikut adalah isi XML lengkap yang diubah:
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
        <stockCheck>
          <productId>&xxe;</productId>
          <storeId>1</storeId>
        </stockCheck>
        ```

5.  **Penyelesaian**:
    *   Kirimkan permintaan yang sudah dimodifikasi dengan mengklik tombol "Forward" di Burp Suite.
    *   Server akan merespons dengan konten dari file `/etc/passwd`, dan lab akan ditandai sebagai "Solved".

## 5. Kesimpulan

Eksploitasi XXE ini berhasil dengan memanfaatkan parser XML yang rentan untuk membaca file sensitif dari sistem server. Dengan mendefinisikan entitas eksternal, penyerang dapat mengakses file lokal, yang menunjukkan risiko kebocoran data yang serius pada aplikasi yang memproses XML secara tidak aman.

## Bukti 
<img width="1919" height="1016" alt="Screenshot 2025-09-10 112346" src="https://github.com/user-attachments/assets/70fa752a-84af-481f-8414-a1543671f5ae" />

<img width="1920" height="1080" alt="Screenshot 2025-09-10 112435" src="https://github.com/user-attachments/assets/f50a1e40-8262-486a-bc64-d5e2858994c6" />

<img width="1919" height="1079" alt="Screenshot 2025-09-10 112553" src="https://github.com/user-attachments/assets/a1af0493-86fb-46b9-a56f-db03f5c30b22" />

<img width="1434" height="654" alt="Screenshot 2025-09-10 112724" src="https://github.com/user-attachments/assets/2a952da2-1f42-4cc2-97d8-70dabf3af9c4" />

<img width="1920" height="1080" alt="Screenshot 2025-09-10 112810" src="https://github.com/user-attachments/assets/fbabaf1d-8768-4301-a4e1-39f131bd0de2" />


