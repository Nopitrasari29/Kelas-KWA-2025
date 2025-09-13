# Juice-Shop Write-up: Find the Easter Egg

## Ringkasan Tantangan

**Judul:** Easter Egg

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Tingkat Kesulitan:** ⭐⭐⭐⭐ (4/6)

Tantangan ini bertujuan untuk menemukan sebuah *Easter Egg* tersembunyi yang memerlukan eksploitasi pada direktori server dan teknik manipulasi file.

## Alat yang Digunakan

*   **Peramban web (browser):** Untuk menjelajahi direktori dan mengunduh file.
*   **Base64 Decoder:** Untuk mendekode pesan rahasia yang tersembunyi.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan menggabungkan dua kerentanan: *Path Traversal* dan *Null Byte Poisoning*.

1.  **Menemukan File Tersembunyi:**
    *   Mengakses direktori FTP pada URL `http://127.0.0.1:3000/ftp`. Di sana, ditemukan file mencurigakan bernama `eastere.gg`.

2.  **Mengelabui Pembatasan File:**
    *   Server hanya mengizinkan pengunduhan file dengan ekstensi `.pdf` atau `.md`.
    *   Untuk melewati filter ini, *null byte* (`%00`) dimasukkan ke URL setelah nama file tetapi sebelum ekstensi yang valid (`.pdf`). Namun, karena server memproses URL dua kali, payload yang berhasil adalah `%2500`.
    *   URL yang digunakan: `http://127.0.0.1:3000/ftp/eastere.gg%2500.pdf`

3.  **Mendapatkan Pesan Rahasia:**
    *   Setelah URL yang benar diakses, file `eastere.gg` berhasil diunduh.
    *   Isi file tersebut adalah pesan yang dienkode dengan Base64.
    *   Dengan mendekode pesan tersebut, kami berhasil menemukan *Easter Egg* yang sesungguhnya.

### Penjelasan Solusi

Tantangan ini berhasil dieksploitasi karena server gagal memvalidasi file dengan benar. Dengan menyisipkan *null byte* yang dienkode dua kali, penyerang berhasil mengelabui sistem verifikasi dan mengunduh file yang seharusnya tidak dapat diakses.

## Saran Perbaikan

Untuk mencegah kerentanan ini, implementasikan langkah-langkah berikut:

1.  **Validasi Ekstensi di Sisi Server:** Selalu periksa ekstensi file di sisi server, dan hindari mengandalkan hanya pada nama file yang dikirimkan oleh klien.
2.  **Pembersihan Input (Sanitization):** Bersihkan semua input, terutama dari karakter khusus seperti *null byte*, sebelum memprosesnya.
3.  **Otorisasi Konten:** Batasi konten yang dapat diakses oleh pengguna dan pastikan hanya jenis file yang diizinkan yang dapat dilayani.

## Bukti 

<img width="1920" height="1080" alt="Screenshot 2025-09-13 145738" src="https://github.com/user-attachments/assets/c2f09c80-5154-464d-b624-564619f05b77" />

<img width="476" height="281" alt="Screenshot 2025-09-13 145845" src="https://github.com/user-attachments/assets/de679ef8-e04b-418a-b7c4-eddc841978dc" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 150316" src="https://github.com/user-attachments/assets/1156a1df-cc31-491f-b97d-e486f81ba412" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 150512" src="https://github.com/user-attachments/assets/286ec527-8a1e-4067-8c75-9815b2bd0d84" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 150533" src="https://github.com/user-attachments/assets/f7e36073-5b7a-44c2-908c-99b692f9c8bc" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 150546" src="https://github.com/user-attachments/assets/eb9a641d-b627-4e56-8ac3-5963d8855141" />


