# Juice-Shop Write-up: Supply Chain Attack

## Challenge Overview

**Title:** Supply Chain Attack
**Category:** Vulnerable Components
**Difficulty:** ⭐⭐⭐⭐⭐ (5/6)

Tantangan ini bertujuan untuk mengidentifikasi paket yang terkompromi dalam dependensi proyek Juice Shop, yang merupakan simulasi dari serangan *supply chain* pada ekosistem *development tool*.

## Metodologi dan Solusi

### Step 1: Menganalisis Dependensi Proyek

Langkah awal adalah mendapatkan daftar lengkap semua *library* yang digunakan, termasuk yang hanya digunakan untuk pengembangan (*development dependencies*).

* **Aksi:** Mengakses dan meninjau file cadangan pengembang (**`package.json.bak`**).
* **Fokus:** Pemeriksaan dilakukan pada bagian `dependencies` dan `devDependencies` karena paket-paket pengembangan sering menjadi target serangan *supply chain*.

### Step 2: Mengidentifikasi Paket yang Rentan

Dengan meninjau daftar `devDependencies` dari file `package.json.bak`, dilakukan pencarian terhadap paket-paket yang memiliki riwayat kerentanan terkait serangan *supply chain*.

* **Penemuan Kunci:** Di antara daftar paket, ditemukan entri untuk paket `eslint-scope`.
* **Versi Rentan:** Paket yang digunakan adalah **`eslint-scope`** versi **`3.7.2`**.

### Step 3: Penelitian Bukti Kerentanan

Pencarian *online* dilakukan menggunakan nama paket dan isu *supply chain* untuk mengonfirmasi kerentanan.

* **Detail Kerentanan:** Ditemukan bahwa `eslint-scope@3.7.2` adalah versi yang terkompromi. Kerentanan tersebut memungkinkan penyerang mengeksekusi kode berbahaya, yang dalam insiden nyata digunakan untuk mencuri **NPM credentials** (tokens).
* **Bukti Asli:** Laporan insiden ini didokumentasikan dalam sebuah *issue* di GitHub.

### Step 4: Melaporkan Bukti Kerentanan (Penyelesaian Challenge)

Tantangan diselesaikan dengan mengirimkan URL dari laporan insiden keamanan asli ke tim pengembang Juice Shop melalui formulir *feedback*.

* **URL Bukti:** `https://github.com/eslint/eslint-scope/issues/39`
* **Aksi Pelaporan:** URL ini dimasukkan ke kolom Komentar/Pesan di halaman **Customer Feedback** / **Contact Us** dan dikirimkan.

Setelah pelaporan URL yang benar, sistem Juice Shop memvalidasi temuan tersebut, dan *challenge* **"Supply Chain Attack"** dinyatakan selesai.

## Rekomendasi Keamanan

1.  **Audit Dependensi Secara Rutin:** Harus ada audit reguler terhadap *dependencies* dan *devDependencies* menggunakan alat otomatis (seperti Snyk atau Dependabot) untuk mendeteksi paket yang dikompromikan atau usang.
2.  **Verifikasi Sumber:** Selalu verifikasi sumber paket sebelum digunakan, dan hindari penggunaan versi yang telah dikompromikan atau dilaporkan rentan.
3.  **Pembaruan Segera:** Segera lakukan *upgrade* atau ganti paket `eslint-scope` ke versi yang telah menambal kerentanan tersebut.

----

## Bukti 
<img width="961" height="961" alt="Screenshot 2025-10-14 143532" src="https://github.com/user-attachments/assets/90440a00-1df7-4b38-aa39-e551a12db4ca" />

<img width="1920" height="1080" alt="Screenshot 2025-10-14 143937" src="https://github.com/user-attachments/assets/73437263-79ad-4141-afa8-0a8ab10391f7" />

<img width="1920" height="1080" alt="Screenshot 2025-10-14 143943" src="https://github.com/user-attachments/assets/386f9fec-1a3a-488c-811b-10e35ec05a3d" />
