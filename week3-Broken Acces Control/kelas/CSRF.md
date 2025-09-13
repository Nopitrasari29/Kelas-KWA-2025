# Juice-Shop Write-up: CSRF (Cross-Site Request Forgery)

## Ringkasan Tantangan

**Judul:** CSRF (Cross-Site Request Forgery)

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Kesulitan:** ⭐⭐⭐ (3/6)

Tantangan ini melibatkan eksploitasi kerentanan *Cross-Site Request Forgery* (CSRF) untuk mengubah nama pengguna di platform OWASP Juice Shop tanpa persetujuan pengguna.

## Alat yang Digunakan

*   **Peramban web (browser):** Untuk berinteraksi dengan aplikasi dan menjalankan kode serangan.
*   **Burp Suite:** Digunakan untuk menganalisis dan mencegat permintaan HTTP.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan mengidentifikasi titik akhir yang rentan dan membuat halaman web palsu yang secara otomatis mengirimkan permintaan atas nama pengguna yang sudah login.

1.  **Mengidentifikasi Titik Serangan:**
    *   Menggunakan Burp Suite, permintaan `POST` yang dibuat saat mengubah nama pengguna berhasil ditangkap.
    *   Titik akhir yang rentan diidentifikasi: `http://127.0.0.1:3000/profile` dengan parameter `username`.

2.  **Membuat Payload Serangan CSRF:**
    *   Sebuah halaman HTML sederhana dibuat. Halaman ini berisi formulir tersembunyi yang akan secara otomatis mengirimkan permintaan ke URL yang rentan.
    *   **Payload HTML:**
        ```html
        <html>
          <body>
            <form action="http://127.0.0.1:3000/profile" method="POST">
              <input type="hidden" name="username" value="HelloFromCSRF" />
            </form>
            <script>document.forms.submit();</script>
          </body>
        </html>
        ```

3.  **Menjalankan Serangan:**
    *   Payload HTML tersebut disimpan sebagai file lokal (`csrf-attack.html`).
    *   File tersebut dibuka di browser yang sama dengan sesi Juice Shop yang sudah login.

### Penjelasan Solusi

Serangan CSRF ini berhasil karena server tidak memvalidasi permintaan dengan token anti-CSRF. Aplikasi menganggap permintaan yang datang dari browser yang sudah login sebagai permintaan yang sah, meskipun permintaan itu berasal dari halaman lain.

## Saran Perbaikan

Untuk melindungi dari CSRF, aplikasi harus:

*   **Menggunakan Token Anti-CSRF:** Setiap formulir harus menyertakan token yang dihasilkan server dan divalidasi saat pengiriman.
*   **Mengonfigurasi Same-Site Cookies:** Atur cookie agar hanya dikirim dalam permintaan yang berasal dari situs yang sama.

## Bukti 

<img width="1920" height="1080" alt="Screenshot 2025-09-13 151505" src="https://github.com/user-attachments/assets/b7941cea-f419-4e5c-b127-3ca31f021482" />

<img width="1917" height="1079" alt="image" src="https://github.com/user-attachments/assets/ecade346-0c92-4895-9d7b-ea4c6568dbb5" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 151646" src="https://github.com/user-attachments/assets/d7107a89-c969-4863-96f7-663b6c8f3842" />
