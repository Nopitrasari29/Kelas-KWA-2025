# Juice-Shop Write-up: Forged Review

## Ringkasan Tantangan

**Judul:** Forged Review

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Tingkat Kesulitan:** ⭐⭐⭐ (3/6)

Tantangan ini bertujuan untuk mengeksploitasi kerentanan kontrol akses yang memungkinkan pengguna mengirimkan ulasan atas nama pengguna lain, menunjukkan kelemahan dalam sistem manajemen identitas.

## Alat yang Digunakan

*   **Burp Suite:** Digunakan untuk mencegat dan memanipulasi permintaan HTTP yang dikirim dari browser ke server.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan memanfaatkan kerentanan *Insecure Direct Object Reference* (IDOR), di mana parameter identifikasi (`author`) dapat diubah untuk memalsukan identitas.

1.  **Menyiapkan dan Menangkap Permintaan:**
    *   Menggunakan akun biasa (bukan admin), kirimkan ulasan melalui halaman yang tersedia.
    *   Dengan Burp Suite, permintaan `POST` yang berisi data ulasan dicegat.

2.  **Memanipulasi Nama Penulis:**
    *   Di Burp Suite, di bagian body permintaan, nilai `author` diubah. Nilai `author` milik akun biasa diganti dengan nama pengguna lain, misalnya `admin@juice-sh.op`.
    *   Permintaan yang sudah dimodifikasi ini kemudian diteruskan ke server.

3.  **Verifikasi:**
    *   Ulasan yang dikirimkan berhasil tercatat di sistem seolah-olah berasal dari akun administrator.

### Penjelasan Solusi

Tantangan ini berhasil diselesaikan karena server gagal memvalidasi apakah pengguna yang mengirimkan ulasan adalah pengguna yang sama dengan yang tercantum di parameter `author`. Kerentanan ini menunjukkan kurangnya verifikasi identitas di sisi server, yang memungkinkan pengguna dengan izin rendah melakukan tindakan atas nama pengguna lain.

## Saran Perbaikan

Untuk mencegah kerentanan ini, langkah-langkah berikut harus dipertimbangkan:

1.  **Validasi Sesi yang Lebih Kuat:** Pastikan server selalu membandingkan ID pengguna dalam permintaan dengan ID pengguna yang terkait dengan sesi saat ini.
2.  **Hapus ID yang Dapat Dimanipulasi:** Jangan sertakan ID pengguna di dalam permintaan yang dikirimkan oleh klien. ID harus diambil dari data sesi di sisi server.
3.  **Validasi Kontrol Akses Berbasis Peran:** Terapkan validasi ketat untuk memastikan bahwa pengguna hanya dapat melakukan tindakan yang sesuai dengan peran mereka, tanpa bergantung pada data yang dikirimkan oleh klien.

## Bukti 
<img width="1905" height="1029" alt="Screenshot 2025-09-13 135404" src="https://github.com/user-attachments/assets/05a57a57-3d4d-477a-98b5-0896c3bc9f76" />

<img width="896" height="246" alt="Screenshot 2025-09-13 135448" src="https://github.com/user-attachments/assets/488ef65f-cf08-41b4-8aec-43df5471e645" />

<img width="1914" height="541" alt="Screenshot 2025-09-13 135528" src="https://github.com/user-attachments/assets/d743b057-85dc-466c-aefc-74ee806d86d2" />
