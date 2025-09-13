# PortSwigger Write-up: User ID controlled by request parameter

## Ringkasan Tantangan

**Judul:** User ID controlled by request parameter, with unpredictable user IDs

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Kesulitan:** Apprentice

**Link Lab:** [https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-unpredictable-user-ids](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-unpredictable-user-ids)

Tantangan ini bertujuan untuk mengeksploitasi kerentanan *Insecure Direct Object Reference* (IDOR) untuk melihat profil pengguna lain. Meskipun ID pengguna bersifat acak dan sulit ditebak, tantangan ini mengajarkan cara menemukan ID tersebut dari sumber lain dan memanipulasinya untuk mendapatkan akses.

## Alat yang Digunakan

*   Peramban web (browser): Untuk menavigasi aplikasi dan memanipulasi URL.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan menemukan ID pengguna lain yang bocor di halaman publik, lalu menggunakan ID itu untuk mengakses profil pengguna tersebut.

1.  **Mendapatkan ID Pengguna yang Bocor:**
    *   Login ke lab dengan kredensial `wiener` dan `peter`.
    *   Dari halaman utama, cari ulasan atau postingan oleh pengguna bernama `carlos`, lalu klik pada namanya.
    *   URL di browser akan berubah dan menampilkan ID unik `carlos` di parameternya (`.../blogs?userId=[ID_carlos]`).

2.  **Akses Profil `carlos`:**
    *   Buka halaman profil Anda sendiri (*My account*). URL-nya akan berisi ID Anda.
    *   Ganti ID pengguna Anda di URL dengan ID `carlos` yang sudah Anda salin.
    *   Contoh: `http://[lab-domain]/my-account?id=[ID_carlos]`

3.  **Mengambil Kunci API:**
    *   Setelah Anda menekan Enter, halaman akan memuat profil `carlos`, di mana Anda dapat menemukan dan menyalin *API key*-nya.
    *   Masukkan kunci ini ke kolom solusi lab untuk menyelesaikannya.

### Penjelasan Solusi

Tantangan ini berhasil dieksploitasi karena server gagal memvalidasi ID pengguna dalam permintaan `GET`. Meskipun ID bersifat acak dan sulit ditebak, aplikasi membocorkannya di halaman publik. Kekurangan ini menunjukkan bahwa server mempercayai ID yang diberikan oleh klien, tanpa memeriksa apakah ID tersebut sesuai dengan sesi pengguna yang sedang aktif.

## Saran Perbaikan

Untuk mencegah kerentanan ini, implementasikan langkah-langkah berikut:

1.  **Validasi Sesi di Sisi Server:** Setiap permintaan yang melibatkan ID pengguna harus divalidasi di sisi server untuk memastikan ID tersebut cocok dengan pengguna yang sedang login.
2.  **Gunakan ID yang Sulit Ditebak:** Meskipun ID yang acak (UUID) digunakan, hindari membocorkannya di URL publik atau di halaman yang dapat diakses oleh pengguna lain.
3.  **Hapus ID dari URL:** Hindari menaruh ID sensitif di URL. Sebaiknya, gunakan data sesi di sisi server untuk mengidentifikasi pengguna.

## Bukti
<img width="1920" height="1080" alt="Screenshot 2025-09-13 194526" src="https://github.com/user-attachments/assets/4b0680c4-4b03-443c-a930-e43840f49b82" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 194842" src="https://github.com/user-attachments/assets/065fb9e7-51ad-4f92-869b-bcd8e14ed033" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 195213" src="https://github.com/user-attachments/assets/41c59d28-5231-4529-bdb8-e347a0e6c9c4" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 195505" src="https://github.com/user-attachments/assets/1a079a3f-ec9d-451c-8f2c-27e0664d3436" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 195605" src="https://github.com/user-attachments/assets/bc6b3270-4c79-4546-81a8-99b83c36fc73" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 195612" src="https://github.com/user-attachments/assets/bce8eaea-d580-4899-ad41-f9b55e8088e5" />
