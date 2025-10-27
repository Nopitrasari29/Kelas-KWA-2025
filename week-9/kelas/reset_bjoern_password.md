
# Juice-Shop Write-up: Reset Bjoern's Password

## Challenge Overview

**Title:** Reset Bjoern's Password
**Category:** OSINT/Forensic
**Difficulty:** ⭐⭐⭐⭐⭐

Tantangan ini memerlukan *Open Source Intelligence* (OSINT) mendalam untuk menemukan *postal code* historis Bjoern yang spesifik, yang berfungsi sebagai jawaban pertanyaan keamanan untuk mereset kata sandinya.

## Tools Used

* **Web Browser:** Untuk mengakses fitur *reset password* dan melakukan pencarian OSINT.
* **Situs Pencarian Data Historis:** Untuk menemukan *postal code* lama Jerman.

---

## Metodologi dan Solusi

### 1. Identifikasi Target dan Pertanyaan

Langkah awal adalah mendapatkan detail *reset* yang dibutuhkan.

* **Email Target:** `bjoern@juice-sh.op`
* **Pertanyaan Keamanan:** "Your ZIP/postal code when you were a teenager?"

### 2. Melakukan OSINT dan Forensik Data

Proses ini melibatkan korelasi informasi pribadi Bjoern yang tersedia di publik dengan data historis kode pos Jerman.

* **Aksi (OSINT Lokasi):** Melalui profil media sosial (contoh: [https://www.facebook.com/bjoern.kimminich](https://www.facebook.com/bjoern.kimminich)), Bjoern diidentifikasi berasal dari **Uetersen, Jerman**.
* **Aksi (Forensik Data Historis):** Karena *ZIP code* saat ini gagal, pencarian dialihkan ke data kode pos sebelum reformasi Jerman (pre-1993) melalui sumber data historis (contoh *tool* seperti `http://www.alte-postleitzahlen.de/uetersen`).
* **Hasil Kunci:** *ZIP code* lama Uetersen adalah **W-2082** (*W* untuk *West Germany*).

### 3. Eksploitasi Format Kode Pos

*Write-up* yang berhasil mengungkap bahwa *server* hanya menerima varian kode pos yang ditulis lengkap (*written out variation*).

* **Jawaban yang Diujikan:** Mengganti 'W-' menjadi 'West-' menghasilkan jawaban yang diterima sistem.
* **Jawaban Kunci:** **`West-2082`**

### 4. Mereset Kata Sandi

Jawaban akhir digunakan pada halaman *Forgot Password*.

* **Aksi:** Mengakses halaman **Forgot Password**.
* **Data yang Digunakan:**
    * **Email:** `bjoern@juice-sh.op`
    * **Jawaban Keamanan:** **`West-2082`**
    * **Kata Sandi Baru:** `BjoernNewP@ss`
* **Penyelesaian:** Reset berhasil, kata sandi Bjoern diubah, dan *challenge* selesai.

## Rekomendasi Keamanan

1.  **Hapus Pertanyaan Keamanan:** Mekanisme *reset* *password* harus menggunakan *email-based reset link* untuk memverifikasi identitas, bukan pertanyaan keamanan yang rentan terhadap OSINT.
2.  **Batasi Informasi Publik:** Pengguna (terutama *developer* atau admin) harus membatasi informasi pribadi historis (*postal code*, tanggal lahir, dll.) yang dibagikan secara publik.

## Bukti 

<img width="275" height="328" alt="Screenshot 2025-10-27 212152" src="https://github.com/user-attachments/assets/acd72ae3-a589-436c-bfd2-85eb1737f38e" />

<img width="957" height="481" alt="Screenshot 2025-10-27 212249" src="https://github.com/user-attachments/assets/ac381777-34d7-4bd9-9db2-470e19b511d8" />

<img width="955" height="474" alt="Screenshot 2025-10-27 212552" src="https://github.com/user-attachments/assets/d6e952f9-473c-4b8d-9b33-c6eebee7e9a0" />

<img width="957" height="444" alt="Screenshot 2025-10-27 212824" src="https://github.com/user-attachments/assets/18c69886-5beb-4934-97b7-dbf0b791e88b" />
