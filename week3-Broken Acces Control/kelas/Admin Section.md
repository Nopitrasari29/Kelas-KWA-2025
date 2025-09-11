# Juice Shop Write-up: Admin Section

## Ringkasan Tantangan

**Judul:** Admin Section

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Tingkat Kesulitan:** ⭐⭐ (2/6)

Tantangan ini menuntut kita untuk menemukan sebuah bagian administrasi yang tersembunyi. Tantangan ini secara efektif menunjukkan kerentanan di mana fungsionalitas sensitif tidak dilindungi dengan benar, melainkan hanya disembunyikan dengan mengandalkan kerahasiaan URL.

## Alat yang Digunakan

*   Peramban web (browser)

## Metodologi dan Solusi

Proses untuk menyelesaikan tantangan ini sangat sederhana dan tidak memerlukan alat khusus:

1.  **Mendeteksi URL Tersembunyi:** Kami memulai dengan meninjau kode sumber halaman utama. Tujuannya adalah mencari jalur URL yang mungkin ditinggalkan terbuka oleh pengembang.
2.  **Mengakses URL Tersembunyi:** Kami berhasil menemukan alamat URL yang mengarah ke bagian administrasi. Rute ini ditemukan di salah satu file JavaScript utama.
3.  **Memverifikasi Solusi:** Kami langsung mengakses URL yang ditemukan, yaitu `http://127.0.0.1:3000/#/administration`. Meskipun server memblokir akses dan menampilkan kesalahan `403 Forbidden`, lab ini menganggap tantangan selesai karena kami berhasil mengakses rute yang seharusnya tidak dapat ditemukan.

### Penjelasan Solusi

Tantangan ini berhasil diselesaikan dengan mengeksploitasi kelalaian keamanan yang umum, yaitu mengandalkan kerahasiaan URL sebagai satu-satunya bentuk keamanan. Siapa pun yang dapat meninjau kode sumber dapat menemukan jalur tersembunyi tersebut. Ini menunjukkan bahwa perlindungan harus diterapkan di sisi server untuk memvalidasi izin pengguna sebelum mengizinkan akses ke area sensitif.

## Saran Perbaikan

Untuk mencegah kerentanan semacam ini, berikut adalah beberapa langkah mitigasi:

1.  **Pemisahan Lingkungan:** Pastikan lingkungan pengembangan dan produksi dipisahkan secara ketat.
2.  **Penerapan Kontrol Akses:** Terapkan aturan kontrol akses yang kuat untuk memastikan fungsionalitas sensitif hanya dapat diakses oleh pengguna yang berwenang.
3.  **Audit Keamanan Berkala:** Lakukan audit keamanan dan pengujian penetrasi secara rutin untuk menemukan dan memperbaiki kerentanan serupa.

## Bukti 
<img width="1919" height="1079" alt="Screenshot 2025-09-11 105109" src="https://github.com/user-attachments/assets/a3c83cba-cb66-4d8b-8c79-33f0291dd73e" />

<img width="1920" height="1080" alt="Screenshot 2025-09-11 105245" src="https://github.com/user-attachments/assets/76cdf03e-c386-4dab-b001-a6ba5ea98ca3" />

<img width="1919" height="561" alt="Screenshot 2025-09-11 105846" src="https://github.com/user-attachments/assets/cbaf387c-d268-40ea-b4b4-8a439fa864f7" />

<img width="1919" height="1068" alt="Screenshot 2025-09-11 111231" src="https://github.com/user-attachments/assets/e58a3e4c-f7c5-4b73-9665-3ce6544b86f1" />



