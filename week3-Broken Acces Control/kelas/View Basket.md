# Juice-Shop Write-up: View Basket

## Ringkasan Tantangan

**Judul:** View Basket

**Kategori:** Kontrol Akses yang Rusak (Broken Access Control)

**Tingkat Kesulitan:** ⭐⭐ (2/6)

Tantangan ini bertujuan untuk mengeksploitasi kerentanan kontrol akses yang memungkinkan pengguna melihat detail keranjang belanja milik pengguna lain dengan memanipulasi identifikasi pengguna di dalam permintaan web.

## Alat yang Digunakan

*   **Burp Suite:** Digunakan untuk mencegat dan memodifikasi permintaan HTTP yang dikirim dari browser ke server.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan memanfaatkan kerentanan *Insecure Direct Object Reference* (IDOR), di mana ID keranjang yang ada di URL dapat diubah untuk melihat data yang seharusnya tidak dapat diakses.

1.  **Menangkap Permintaan HTTP:**
    *   Menggunakan Burp Suite, permintaan HTTP untuk melihat keranjang belanja sendiri ditangkap.
    *   Permintaan ini terlihat seperti `GET /rest/basket/1`, di mana angka `1` adalah ID keranjang yang ditargetkan.

2.  **Memodifikasi ID Keranjang:**
    *   ID keranjang dalam permintaan yang ditangkap diubah dari `1` menjadi `2` (atau angka lain).

3.  **Melihat Hasil:**
    *   Permintaan yang sudah dimodifikasi diteruskan ke server.
    *   Respons dari server menunjukkan detail keranjang belanja pengguna lain, yang membuktikan adanya kerentanan.

### Penjelasan Solusi

Tantangan ini berhasil diselesaikan dengan mengubah ID keranjang di URL, yang seharusnya tidak mungkin terjadi jika mekanisme kontrol akses yang tepat sudah diterapkan. Ini memungkinkan akses ke informasi keranjang pribadi pengguna lain yang sensitif.

## Saran Perbaikan

Untuk mengurangi kerentanan ini, langkah-langkah berikut harus dipertimbangkan:

1.  **Validasi Sesi Pengguna:** Setiap permintaan untuk mengakses informasi sensitif, seperti keranjang belanja, harus divalidasi dengan sesi pengguna untuk memastikan bahwa pengguna tersebut memang berwenang untuk melihat data tersebut.
2.  **Gunakan Mekanisme Kontrol Akses yang Kuat:** Terapkan kontrol akses berbasis peran (RBAC) atau berbasis atribut (ABAC) untuk menegakkan izin yang ketat pada akses data.
3.  **Audit Keamanan Berkala:** Lakukan audit keamanan dan pengujian penetrasi secara teratur untuk menemukan dan memperbaiki masalah kontrol akses.

## Bukti 
<img width="1919" height="1079" alt="Screenshot 2025-09-11 111939" src="https://github.com/user-attachments/assets/6a78de72-d8c9-4bd0-ab15-1295985f65fa" />

<img width="1919" height="1079" alt="Screenshot 2025-09-11 112047" src="https://github.com/user-attachments/assets/ff8ebe04-614b-40fa-912f-ee2335314bcc" />

<img width="1919" height="537" alt="Screenshot 2025-09-11 112107" src="https://github.com/user-attachments/assets/769e862e-4bd0-4fe4-9cfa-7e59ce5f1afe" />

<img width="1919" height="929" alt="Screenshot 2025-09-11 112205" src="https://github.com/user-attachments/assets/d1f9128d-3868-4a75-97f1-5c2cd2c7040a" />
