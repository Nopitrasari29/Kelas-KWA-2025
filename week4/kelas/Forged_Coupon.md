# Write-up Juice-Shop: Kupon Palsu (Forged Coupon)

**Kategori:** Kriptografi

**Tingkat Kesulitan:** ⭐⭐⭐⭐⭐⭐ (6/6)

## Ikhtisar Tantangan

Tantangan ini menguji kemampuan kita dalam rekayasa balik (*reverse engineering*) untuk mengungkap rahasia di balik kode kupon Juice Shop. Tujuannya adalah menemukan cara kode kupon itu dibuat, lalu memalsukan kupon baru yang memberikan diskon minimal 80%.

## Metode dan Solusi

Penyelesaian tantangan ini melibatkan investigasi cerdas, menganalisis file-file tersembunyi, dan mereplikasi proses pembuatan kupon.

### 1. Pembongkaran Informasi Rahasia

Langkah pertama adalah mencari petunjuk yang tidak sengaja bocor di server. Dengan menelusuri direktori `/ftp`, ditemukan beberapa file cadangan dengan ekstensi `.bak`. File-file ini seharusnya tidak bisa diakses, tetapi terhalang oleh filter yang hanya mengizinkan file `.md` dan `.pdf`.

Untuk melewati filter ini, digunakan trik *Poison Null Byte* (`%2500`). Dengan menambahkan kode ini di URL, kita menipu server agar mengabaikan filter dan memberikan akses ke file aslinya. Misalnya, URL `http://localhost:3000/ftp/package.json.bak%2500.md` berhasil membuka file yang seharusnya terkunci.

### 2. Menemukan Algoritma Misterius

Setelah berhasil mendapatkan file-file tersembunyi, dua informasi kunci berhasil ditemukan:
*   `coupons_2013.md.bak`: Berisi daftar kupon lama yang terlihat acak.
*   `package.json.bak`: Mengungkap daftar library yang digunakan, termasuk sebuah library bernama `Z85`.

Petunjuk ini sangat krusial. Dengan menggunakan `Z85` sebagai dasar, kupon lama (`n<MibgC7sn`) didekode menggunakan alat online seperti Cryptii. Hasilnya, sebuah teks yang mudah dibaca, `JAN13-10`, berhasil ditemukan.

Ini mengungkap pola rahasia di balik kupon: **{3 Huruf Bulan}{2 Digit Tahun}-{Persentase Diskon}**.

### 3. Membuat Kupon Palsu yang Sempurna

Untuk membuat kupon yang dapat digunakan, kita harus memenuhi dua syarat: diskon minimal 80% dan tanggal yang sesuai dengan waktu server.

Tanggal server ditemukan dengan menganalisis token otentikasi JWT yang disimpan di browser. Token ini berisi *timestamp* `iat` (*Issued At*) yang menunjukkan waktu server. Dengan mengubah *timestamp* tersebut menggunakan Epoch Converter, ditemukan bahwa tanggal server saat itu adalah September 2025.

Dengan semua informasi yang sudah lengkap, *plaintext* kupon yang baru dibuat:
*   **Plaintext:** `SEP25-80` (untuk diskon 80% di bulan September 2025).

Selanjutnya, *plaintext* ini di-*encode* kembali menggunakan `Z85` untuk menghasilkan kode kupon yang valid, seperti `q:<I<rH7Z"w`.

### 4. Verifikasi dan Penyelesaian

Kupon palsu yang baru dibuat dimasukkan ke keranjang belanja Juice Shop. Sistem mengenali kupon tersebut sebagai kupon yang sah, diskon 80% berhasil diterapkan, dan tantangan ditandai sebagai selesai.

## Kerentanan dan Pencegahan

Tantangan ini menunjukkan kelemahan serius dalam sistem kupon. Kode kupon hanya mengandalkan skema pengkodean yang bisa dibalik, bukan metode enkripsi yang aman secara kriptografis.

Untuk mencegah kerentanan ini, kupon harus dibuat menggunakan randomisasi yang kuat dan dilindungi dengan tanda tangan digital (HMAC). Dengan begitu, hanya server yang sah yang bisa membuat kupon valid, dan penyerang tidak akan bisa memalsukannya. Selain itu, informasi internal seperti library yang digunakan dan tanggal server tidak seharusnya diekspos di file yang dapat diakses publik.


## Bukti
<img width="1919" height="453" alt="Screenshot 2025-09-18 124753" src="https://github.com/user-attachments/assets/d61749c7-aa3a-4d41-a7ff-35442a9df6b8" />

<img width="963" height="558" alt="Screenshot 2025-09-18 124816" src="https://github.com/user-attachments/assets/6579685a-4960-4569-a1d7-0d229e99bc1f" />

<img width="1623" height="458" alt="Screenshot 2025-09-18 125007" src="https://github.com/user-attachments/assets/ee9c0e1c-be73-44f2-bf01-b5eca61fd768" />

<img width="1919" height="937" alt="Screenshot 2025-09-18 125202" src="https://github.com/user-attachments/assets/3b728e01-2dfb-4f4a-b223-9667970efedb" />

<img width="1919" height="860" alt="Screenshot 2025-09-18 125755" src="https://github.com/user-attachments/assets/6d2a14c2-8285-4224-b7ef-3539a55fa2ba" />

<img width="1920" height="1080" alt="Screenshot 2025-09-19 231944" src="https://github.com/user-attachments/assets/f2175269-c359-4474-be85-b5cd984e87db" />

<img width="1920" height="1080" alt="Screenshot 2025-09-19 232102" src="https://github.com/user-attachments/assets/2d05390f-5a7a-4074-b3f1-0f4c3c27480d" />

<img width="1919" height="927" alt="Screenshot 2025-09-19 232154" src="https://github.com/user-attachments/assets/4b11acf7-3a54-4b4e-b453-3432a3f06d2c" />

<img width="1920" height="1080" alt="Screenshot 2025-09-19 232336" src="https://github.com/user-attachments/assets/82a40f31-2f80-4d9c-9f5a-f0b1a20dfbb7" />

<img width="1920" height="1080" alt="Screenshot 2025-09-19 232601" src="https://github.com/user-attachments/assets/9a3f080f-e7cf-4796-ac10-c6a2a9247604" />

<img width="1920" height="1080" alt="Screenshot 2025-09-19 232830" src="https://github.com/user-attachments/assets/b1aa7655-080f-418c-93f5-b1913ee63fdd" />

