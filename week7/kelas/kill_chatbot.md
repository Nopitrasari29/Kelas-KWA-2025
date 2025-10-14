# Juice-Shop Write-up: Kill Chatbot

## Challenge Overview

**Title:** Kill Chatbot
**Category:** Application Logic
**Difficulty:** ⭐⭐⭐⭐⭐ (5/6)

Tantangan ini bertujuan untuk menonaktifkan fitur *chatbot* yang dikenal sebagai **`juicy-chat-bot`** dengan mengeksploitasi kerentanan *Code Injection* pada input pengguna, memaksa proses *chatbot* untuk *hang* atau *crash*.

## Tools Used

  * **Web Browser:** Untuk berinteraksi dengan *chatbot* dan mengeksekusi *payload*.
  * **Code Analysis (Berdasarkan *Write-up* Lama):** Untuk mengidentifikasi bahwa *chatbot* menggunakan lingkungan eksekusi skrip dinamis (diduga **`vm2`**) yang rentan terhadap *unsanitized input*.

## Metodologi dan Solusi

### 1\. Mengidentifikasi Vektor Eksploitasi

Berdasarkan analisis fungsi *chatbot* (seperti yang diuraikan dalam petunjuk tantangan), diketahui bahwa masukan pengguna (khususnya, nama) disisipkan langsung ke dalam *string* kode JavaScript yang akan dieksekusi di lingkungan *sandbox* *server*.

  * **Pemicu:** Ketika *chatbot* meminta nama pengguna: **"I'm sorry I didn't get your name. What shall I call you?"**
  * **Kerentanan:** Input pengguna (`${name}`) disisipkan ke dalam fungsi yang rentan (misalnya, `this.factory.run('users.addUser("${token}", "${name}")')`) tanpa sanitasi.

### 2\. Merancang Payload untuk Denial of Service (DoS)

Karena *payload* umum seperti `process=null` tidak efektif pada versi Juice Shop yang digunakan, strategi diubah menjadi **membuat proses *chatbot* *hang* (menggantung)** secara tak terbatas, yang secara fungsional akan "membunuh" bot.

  * **Tujuan:** Menyuntikkan *infinite loop* (`while(true){}`) ke dalam skrip eksekusi *chatbot*.
  * **Payload yang Dibuat:** *Payload* dirancang untuk menutup *string* masukan, menyuntikkan *loop*, dan mengomentari sisa kode.

<!-- end list -->

```plaintext
testname"); while(true){} //
```

### 3\. Eksekusi Payload

  * **Aksi:** *Payload* tersebut disalin dan dimasukkan langsung ke kolom input pesan *chatbot* saat bot meminta nama.
  * **Eksekusi Kode:** Ketika dikirim, *payload* diterjemahkan menjadi skrip yang dieksekusi di lingkungan *sandbox* *chatbot*, memaksa *thread* eksekusi untuk memasuki *infinite loop*.

### 4\. Verifikasi dan Penyelesaian

Setelah pengiriman *payload* `testname"); while(true){} //`:

  * **Indikasi Visual:** *Chatbot* tidak memberikan respons normal terhadap *payload* tersebut (atau *payload* berikutnya), mengonfirmasi bahwa proses *thread* di *backend* telah *hang* dan *chatbot* dinonaktifkan.
  * **Validasi Challenge:** Tindakan ini berhasil memicu sistem *score board* Juice Shop. *Challenge* **"Kill Chatbot"** berhasil diselesaikan karena *chatbot* tidak dapat lagi berfungsi.

## Rekomendasi Keamanan

1.  **Stop Menggunakan Eksekusi Dinamis:** Hindari penggunaan fungsi yang mengevaluasi *string* sebagai kode (*exec* atau `vm.run`) dengan *user input* di dalamnya.
2.  **Sanitasi Input Tingkat Lanjut:** Jika eksekusi dinamis mutlak diperlukan, semua input pengguna harus melewati sanitasi ketat, termasuk melarikan diri (escape) semua tanda kutip, kurung kurawal, atau karakter pemisah pernyataan.
3.  ***Sandbox* yang Tepat:** Jika menggunakan lingkungan *sandbox* seperti `vm2`, pastikan konfigurasi *sandbox* sudah benar dan menggunakan versi terbaru untuk memitigasi *sandbox escape* dan serangan DoS internal.

---

## Bukti
<img width="1920" height="1080" alt="Screenshot 2025-10-14 135411" src="https://github.com/user-attachments/assets/2f5572d1-2668-4a4e-b0c3-bb45f7151e7e" />
