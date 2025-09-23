# Juice-Shop Write-up: Unlock Premium Challenge

## 1. Gambaran Tantangan

**Judul:** Unlock Premium Challenge
**Kategori:** Cryptographic Issues
**Tingkat Kesulitan:** ⭐⭐⭐⭐⭐⭐ (6/6)

Tantangan ini menantang pemain untuk melewati simulasi *paywall premium* dengan menemukan dan menggunakan kunci enkripsi yang bocor. Tujuannya adalah untuk mendekripsi URL rahasia yang tersembunyi, yang akan membuka akses ke konten premium tanpa perlu melakukan pembayaran. Tantangan ini mengilustrasikan risiko serius dari manajemen kunci enkripsi yang buruk.

---

## 2. Metodologi dan Solusi

Pendekatan untuk menyelesaikan tantangan ini melibatkan tiga tahap utama: menemukan kunci, menemukan data terenkripsi, dan mendekripsinya.

### Langkah 1: Mengidentifikasi Kunci Enkripsi

Saya memulai dengan mencari direktori file yang tidak terlindungi, yang umum terjadi karena kesalahan konfigurasi server. Saya berhasil menemukan direktori `/encryptionkeys` yang dapat diakses secara publik. Di dalamnya, ada file bernama `premium.key`. Setelah mengunduh dan menganalisis isinya, saya menemukan dua komponen kunci:

*   **Kunci Enkripsi (Key):** `EA99A61D92D2955B1E9285B55BF2AD42`
*   **Vektor Inisialisasi (IV):** `1337133713371337`

Kedua nilai ini adalah string heksadesimal yang digunakan dalam algoritma enkripsi AES.

### Langkah 2: Menemukan Data Terenkripsi

Data terenkripsi yang berisi URL rahasia disembunyikan dalam komentar HTML di kode sumber halaman utama. Komentar ini berisi string panjang yang di-encode dengan Base64. Saya mengidentifikasi string ini sebagai data yang perlu didekripsi.

*   **Data Terenkripsi (Base64):** `IvLuRfBJYlmStf9XfL6ckJFngyd9LfV1JaaN/KRTPQPidTuJ7FR+D/nkWJUF+0xUF07CeCeqYfxq+OJVVa0gNbqgYkUNvn//UbE7e95C+6e+7GtdpqJ8mqm4WcPvUGIUxmGLTTAC2+G9UuFCD1DUjg==`

### Langkah 3: Dekripsi dan Akses Konten

Dengan semua komponen yang diperlukan, saya menggunakan OpenSSL di Terminal untuk mendekripsi string Base64 tersebut. Saya menggabungkan kunci (`-K`), IV (`-iv`), dan data Base64 (`-a`).

**Perintah OpenSSL:**
```bash
echo "IvLuRfBJYlmStf9XfL6ckJFngyd9LfV1JaaN/KRTPQPidTuJ7FR+D/nkWJUF+0xUF07CeCeqYfxq+OJVVa0gNbqgYkUNvn//UbE7e95C+6e+7GtdpqJ8mqm4WcPvUGIUxmGLTTAC2+G9UuFCD1DUjg==" | openssl enc -d -aes-256-cbc -K EA99A61D92D2955B1E9285B55BF2AD42 -iv 1337133713371337 -a -A
```
Perintah ini berhasil mendekripsi data dan mengungkapkan URL rahasia:
`http://localhost:3000/this/page/is/hidden/behind/an/incredibly/high/paywall/that/could/only/be/unlocked/by/sending/1btc/to/us`

Dengan menavigasi ke URL yang didekripsi, saya berhasil mengakses konten premium dan menyelesaikan tantangan.

---

## 3. Kesimpulan dan Pembelajaran

Tantangan "Unlock Premium Challenge" menekankan pada manajemen aset yang ceroboh. Kunci enkripsi yang seharusnya dijaga kerahasiaannya justru disimpan di direktori yang dapat diakses, memungkinkan penyerang untuk menggunakannya demi keuntungan pribadi. Hal ini menyoroti pentingnya:

*   **Penyimpanan Kunci yang Aman:** Kunci enkripsi tidak boleh disimpan di lokasi yang mudah diakses atau disematkan di kode client-side.
*   **Kontrol Akses yang Kuat:** Direktori dan file sensitif harus dilindungi dengan kontrol akses yang ketat untuk mencegah akses tidak sah.
*   **Enkripsi yang Benar:** Meskipun data dienkripsi, jika kunci dan IV-nya bocor, enkripsi menjadi tidak berguna.
  
## Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-23 172347" src="https://github.com/user-attachments/assets/434ad7ae-ca3e-4efc-b34e-87c48e3b40c4" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 172404" src="https://github.com/user-attachments/assets/9386f2e1-ecc0-4b1d-8d28-98e31a708f4c" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 173014" src="https://github.com/user-attachments/assets/cec0228f-0878-4b90-b8b7-4f99a62b3bd9" />

<img width="1920" height="1080" alt="Screenshot 2025-09-23 173221" src="https://github.com/user-attachments/assets/072a6b66-e85b-4c37-b70e-8e3dd0feabc2" />

<img width="628" height="259" alt="Screenshot 2025-09-23 173751" src="https://github.com/user-attachments/assets/d88b3518-9dfd-4fe2-ba5f-3e13ccf91075" />





