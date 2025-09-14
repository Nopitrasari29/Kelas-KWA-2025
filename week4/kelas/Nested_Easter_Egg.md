# Juice-Shop Write-up: Nested Easter Egg

## Ringkasan Tantangan

**Judul:** Nested Easter Egg

**Kategori:** Masalah Kriptografi (Cryptographic Issues)

**Kesulitan:** ⭐⭐⭐⭐ (4/6)

Tantangan ini menuntut saya untuk mendekripsi pesan rahasia yang tersembunyi di dalam *Easter Egg* lain. Saya harus menggunakan metode kriptografi berlapis untuk menemukan jalur URL rahasia yang akan mengarah ke solusi tantangan.

## Alat yang Digunakan

*   **Peramban web (browser):** Untuk menavigasi aplikasi.
*   **Decoder Base64:** Untuk mendekode pesan pertama.
*   **Alat Dekripsi ROT13:** Untuk mendekripsi pesan kedua.

## Metodologi dan Solusi

Tantangan ini diselesaikan dengan proses dekripsi dua langkah yang logis:

1.  **Mendekode Pesan Base64:**
    *   Saya mengambil string yang dienkode dengan Base64 dari file `eastere.gg` yang saya dapatkan dari tantangan sebelumnya: `L2d1ci9xcmlmL25lci9mYi9zaGFhbC9ndXJsL3V2cS9uYS9ybmZncmUvcnR0L2p2Z3V2YS9ndXIvcm5mZ3JlL3J0dA==`
    *   Dengan menggunakan decoder Base64, saya mendapatkan pesan yang terlihat acak: `/gur/qnf/ner/fb/shaal/gur/uvqva/na/rnfgre/rtt/jvguhva/gur/gur/rnfgre/rtt`

2.  **Mendekripsi Pesan ROT13:**
    *   Saya mengenali bahwa pesan yang terlihat acak itu sebenarnya dienkode dengan cipher ROT13.
    *   Saya menggunakan alat dekripsi ROT13 online untuk mendekripsi pesan tersebut.
    *   Hasilnya adalah jalur URL yang bisa dibaca: `/the/devs/are/so/funny/they/hid/an/easter/egg/within/the/easter/egg`

3.  **Mengakses URL Tersembunyi:**
    *   Saya memasukkan jalur URL yang sudah saya dekripsi ke browser: `http://127.0.0.1:3000/the/devs/are/so/funny/they/hid/an/easter/egg/within/the/easter/egg`.
    *   Setelah mengunjungi URL ini, tantangannya selesai.

### Penjelasan Solusi

Tantangan ini menunjukkan bagaimana data dapat disembunyikan menggunakan beberapa lapisan enkripsi. Solusinya membutuhkan pemikiran logis dan pemahaman tentang cipher sederhana untuk mengungkap pesan rahasia yang sebenarnya adalah sebuah URL.

## Saran Perbaikan

Untuk melindungi data dari dekripsi, aplikasi harus:

1.  **Gunakan Enkripsi Multi-faktor:** Terapkan beberapa lapisan enkripsi dengan kunci atau metode yang berbeda untuk meningkatkan keamanan.
2.  **Ikuti Praktik Terbaik Kriptografi:** Gunakan algoritma enkripsi yang kuat dan non-standar, dan jangan mengandalkan cipher yang mudah dipecahkan seperti ROT13.

## Bukti 
<img width="885" height="585" alt="Screenshot 2025-09-14 193714" src="https://github.com/user-attachments/assets/89077669-1e3f-4e31-b2c8-ae8f84e5b67c" />

<img width="1920" height="1080" alt="Screenshot 2025-09-14 193706" src="https://github.com/user-attachments/assets/ae2967c5-7fbb-4d13-86c6-e5f3626b7d69" />

<img width="1188" height="419" alt="Screenshot 2025-09-14 194330" src="https://github.com/user-attachments/assets/9ba1936a-5a25-439c-b5b6-2288ecb77269" />

<img width="1920" height="1080" alt="Screenshot 2025-09-14 194520" src="https://github.com/user-attachments/assets/42494d2f-e8af-4967-b00c-f036f2d41fd1" />

<img width="1917" height="382" alt="Screenshot 2025-09-14 194542" src="https://github.com/user-attachments/assets/15abc9f4-e46f-40a0-ad78-e35d287d0968" />
