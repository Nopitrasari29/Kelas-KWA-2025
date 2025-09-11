# Juice-Shop Write-up: Forged Feedback

## Ringkasan Tantangan

**Judul:** Forged Feedback

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Tingkat Kesulitan:** ⭐⭐⭐ (3/6)

Tantangan ini bertujuan untuk mengeksploitasi kerentanan kontrol akses yang memungkinkan pengguna mengirimkan feedback atas nama pengguna lain, menunjukkan kelemahan dalam sistem manajemen identitas.

## Alat yang Digunakan

*   **Burp Suite:** Digunakan untuk mencegat dan memanipulasi permintaan HTTP yang dikirim dari browser ke server.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan memanfaatkan kerentanan *Insecure Direct Object Reference* (IDOR), di mana ID pengguna yang ada di permintaan dapat diubah untuk memalsukan identitas.

1.  **Menyiapkan dan Menangkap Permintaan:**
    *   Menggunakan akun biasa (bukan admin), kirimkan feedback melalui halaman yang tersedia.
    *   Dengan Burp Suite, permintaan `POST` yang berisi data feedback dicegat.

2.  **Memanipulasi ID Pengguna:**
    *   Di Burp Suite, di bagian body permintaan, nilai `UserId` diubah. Nilai `UserId` milik akun biasa diganti dengan nilai `1`, yang merupakan ID pengguna admin.
    *   Permintaan yang sudah dimodifikasi ini kemudian diteruskan ke server.

3.  **Verifikasi:**
    *   Feedback yang dikirimkan berhasil tercatat di sistem seolah-olah berasal dari akun administrator.

### Penjelasan Solusi

Tantangan ini berhasil diselesaikan karena server gagal memvalidasi apakah pengguna yang mengirimkan feedback adalah pengguna yang sama dengan yang tercatat di `UserId`. Kerentanan ini menunjukkan kurangnya verifikasi identitas di sisi server, yang memungkinkan pengguna dengan izin rendah melakukan tindakan atas nama pengguna lain.

## Saran Perbaikan

Untuk mencegah kerentanan ini, langkah-langkah berikut harus dipertimbangkan:

1.  **Validasi Sesi yang Lebih Kuat:** Pastikan server selalu membandingkan ID pengguna dalam permintaan dengan ID pengguna yang terkait dengan sesi saat ini.
2.  **Hapus ID yang Dapat Dimanipulasi:** Jangan sertakan ID pengguna di dalam permintaan yang dikirimkan oleh klien. ID harus diambil dari data sesi di sisi server.
3.  **Validasi Kontrol Akses Berbasis Peran:** Terapkan validasi ketat untuk memastikan bahwa pengguna hanya dapat melakukan tindakan yang sesuai dengan peran mereka, tanpa bergantung pada ID yang dikirimkan oleh klien.

## Bukti 
<img width="1919" height="1079" alt="Screenshot 2025-09-11 113723" src="https://github.com/user-attachments/assets/16534fdd-b2be-4acb-b8fc-4a4b536b359c" />

<img width="1912" height="1079" alt="Screenshot 2025-09-11 113746" src="https://github.com/user-attachments/assets/aa104a8a-64d0-4d21-8821-8e5c1ed691e1" />

<img width="1399" height="480" alt="Screenshot 2025-09-11 113828" src="https://github.com/user-attachments/assets/a624c49f-9c77-4140-a6a9-fe4d32eebb20" />

<img width="1919" height="1072" alt="Screenshot 2025-09-11 113925" src="https://github.com/user-attachments/assets/0f3f1597-7007-42e6-8fcf-551df67e1734" />

