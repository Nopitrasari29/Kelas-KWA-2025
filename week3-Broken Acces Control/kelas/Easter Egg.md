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
<img width="1919" height="1079" alt="Screenshot 2025-09-13 144151" src="https://github.com/user-attachments/assets/4c33c1c1-eb71-4789-97c9-5ae06913a217" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 144303" src="https://github.com/user-attachments/assets/558dfac4-71dd-4956-bfa7-a469eb387724" />

<img width="879" height="575" alt="Screenshot 2025-09-13 144511" src="https://github.com/user-attachments/assets/ed1f11fe-d98a-43c8-b216-1aa8cb779134" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 144726" src="https://github.com/user-attachments/assets/f1b404d1-a1de-4398-be8e-3a076613e756" />

<img width="1714" height="553" alt="Screenshot 2025-09-13 144745" src="https://github.com/user-attachments/assets/954de4a4-fa63-4bd2-b74d-68e8e223fc73" />



