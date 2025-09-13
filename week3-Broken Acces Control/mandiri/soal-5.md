# PortSwigger Write-up: User ID controlled by request parameter with data leakage in redirect

## Ringkasan Tantangan

**Judul:** User ID controlled by request parameter with data leakage in redirect

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Kesulitan:** Apprentice

**Link Lab:** [https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-data-leakage-in-redirect](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-data-leakage-in-redirect)

Tantangan ini bertujuan untuk mengeksploitasi kerentanan *Insecure Direct Object Reference* (IDOR) untuk melihat profil pengguna lain. Meskipun ID pengguna bersifat acak dan sulit ditebak, tantangan ini mengajarkan cara menemukan ID tersebut dari sumber lain dan memanipulasinya untuk mendapatkan akses.

## Alat yang Digunakan

*   Peramban web (browser): Untuk menavigasi aplikasi dan memanipulasi URL.
*   **Burp Suite:** Digunakan untuk mencegat respons redirect dan mengambil data yang bocor.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan menemukan ID pengguna lain yang bocor di halaman publik, lalu menggunakan ID itu untuk memanipulasi URL dan menangkap data yang bocor di respons redirect.

1.  **Mendapatkan ID Pengguna yang Bocor:**
    *   Login ke lab dengan kredensial `wiener` dan kata sandi `peter`.
    *   Dari halaman utama, cari ulasan atau postingan oleh pengguna bernama `carlos`, lalu klik pada namanya.
    *   URL di browser akan berubah dan menampilkan ID unik `carlos` di parameternya (`.../blogs?userId=[ID_carlos]`).

2.  **Akses Profil `carlos`:**
    *   Pergi ke halaman profil Anda sendiri (*My account*). URL-nya akan berisi ID Anda.
    *   Ganti ID pengguna Anda di URL dengan ID `carlos` yang sudah Anda salin.
    *   Contoh: `http://[lab-domain]/my-account?id=[ID_carlos]`

3.  **Mengambil Kunci API dari Respons:**
    *   Setelah Anda menekan Enter, browser akan menampilkan profil `carlos`, di mana Anda dapat menemukan dan menyalin *API key*-nya.
    *   Masukkan kunci ini ke kolom solusi lab untuk menyelesaikannya.

### Penjelasan Solusi

Tantangan ini berhasil dieksploitasi karena server gagal memvalidasi ID pengguna dalam permintaan `GET`. Meskipun ID bersifat acak dan sulit ditebak, aplikasi membocorkannya di halaman publik. Kekurangan ini menunjukkan bahwa server mempercayai ID yang diberikan oleh klien, tanpa memeriksa apakah ID tersebut sesuai dengan sesi pengguna yang sedang aktif.

## Saran Perbaikan

Untuk mencegah kerentanan ini, implementasikan langkah-langkah berikut:

1.  **Validasi Sesi di Sisi Server:** Setiap permintaan yang melibatkan ID pengguna harus divalidasi di sisi server untuk memastikan ID tersebut cocok dengan pengguna yang sedang login.
2.  **Hapus ID dari URL:** Hindari menaruh ID sensitif di URL. Sebaiknya, gunakan data sesi di sisi server untuk mengidentifikasi pengguna.

## Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-13 200056" src="https://github.com/user-attachments/assets/5c810c3a-31d7-4b30-b88c-fc7482fcd6cc" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 201425" src="https://github.com/user-attachments/assets/79157a45-d057-408b-8e1f-97926341c1a4" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 201541" src="https://github.com/user-attachments/assets/ee3d8143-acac-420e-a770-bff0c2bbecc1" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 201556" src="https://github.com/user-attachments/assets/5fd54fd2-7a22-488f-a2a5-165d5640a9fb" />

<img width="1920" height="1080" alt="Screenshot 2025-09-13 201621" src="https://github.com/user-attachments/assets/211c4de4-2174-4aa2-b660-c1ab029bc844" />
