# Juice-Shop Write-up: Five-Star Feedback

## Ringkasan Tantangan

**Judul:** Five-Star Feedback

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Tingkat Kesulitan:** ⭐⭐ (2/6)

Tantangan ini bertujuan untuk memanipulasi sistem ulasan pada aplikasi web dengan menghapus semua ulasan yang memiliki rating bintang lima.

## Alat yang Digunakan

*   **Peramban web (browser):** Digunakan untuk berinteraksi dengan dasbor administrasi aplikasi.
*   **Akses ke Panel Admin:** Menggunakan hak akses administrator yang didapatkan dari tantangan sebelumnya.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan memanfaatkan hak akses administrator untuk mengakses dan memodifikasi data yang seharusnya dilindungi.

1.  **Mengakses Panel Admin:**
    *   Menggunakan kredensial administrator yang valid, berhasil masuk ke aplikasi.
    *   Menavigasi ke halaman administrasi yang tersembunyi.

2.  **Mengelola Ulasan:**
    *   Di dalam panel admin, menemukan bagian yang mengelola ulasan pengguna.
    *   Mengidentifikasi semua ulasan yang memiliki rating lima bintang.
    *   Menghapus semua ulasan tersebut satu per satu menggunakan fungsionalitas hapus yang tersedia.

3.  **Penyelesaian:**
    *   Setelah semua ulasan bintang lima terhapus, tantangan secara otomatis ditandai sebagai `"Solved"`.

### Penjelasan Solusi

Tantangan ini berhasil diselesaikan dengan mengeksploitasi mekanisme kontrol akses yang tidak memadai. Kekurangan ini memungkinkan seorang pengguna dengan hak akses admin untuk menghapus data sensitif tanpa pengawasan atau validasi yang tepat.

## Saran Perbaikan

Untuk mengurangi kerentanan ini, langkah-langkah berikut harus dipertimbangkan:

1.  **Kontrol Akses Berbasis Peran (RBAC):** Pastikan hanya personel yang berwenang yang memiliki kemampuan untuk memodifikasi atau menghapus konten yang dibuat oleh pengguna.
2.  **Jejak Audit (Audit Trails):** Terapkan jejak audit yang mencatat semua tindakan administrasi, terutama yang memengaruhi data pengguna, untuk meningkatkan akuntabilitas.
3.  **Validasi Ulang:** Terapkan mekanisme yang mewajibkan administrator untuk memberikan justifikasi saat menghapus konten dalam jumlah besar.

## Bukti 
<img width="1919" height="1079" alt="Screenshot 2025-09-11 112607" src="https://github.com/user-attachments/assets/9a92abcb-f6d5-4d52-bbc3-452e961a60b0" />

<img width="1917" height="1079" alt="Screenshot 2025-09-11 112721" src="https://github.com/user-attachments/assets/5778775e-65e6-4b9a-80e2-31829694985d" />

