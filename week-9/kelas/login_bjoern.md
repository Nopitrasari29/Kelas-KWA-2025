
# Juice-Shop Write-up: Bjoern Login

## Challenge Overview

**Title:** Bjoern Login
**Category:** Broken Authentication
**Difficulty:** ⭐⭐⭐⭐

Tantangan ini diselesaikan dengan mengeksploitasi cacat pada implementasi otentikasi OAuth lama Juice-Shop, di mana *password* pengguna dibuat melalui algoritma yang dapat dibalik (*reversible*) dari alamat email mereka.

## Tools Used

  * **Web Browser:** Untuk interaksi dan *login*.
  * **Developer Tools (Console):** Untuk menjalankan kode JavaScript (*reverse engineering*).

## Metodologi dan Solusi

### 1\. Analisis Algoritma Password

Dengan menginspeksi kode JavaScript aplikasi (misalnya `main.js`), ditemukan bahwa *password* dibuat menggunakan algoritma yang secara tidak aman membalikkan *string* email lalu meng-*encode*-nya ke Base64:

  * **Email Target (Asli, untuk Perhitungan):** `bjoern.kimminich@googlemail.com`
  * **Logika Password:**
    ```javascript
    password: btoa(n.email.split("").reverse().join(""))
    ```

### 2\. Perhitungan dan Verifikasi Kredensial

Kata sandi dihitung berdasarkan email asli Bjoern. Untuk *login*, sistem menerima *alias* `gmail.com`.

  * **Aksi:** Menggunakan **Console** untuk menjalankan perhitungan:
    ```javascript
    var email = "bjoern.kimminich@gmail.com"; // Digunakan sebagai string input untuk perhitungan
    var reversed = email.split("").reverse().join("");
    var password = btoa(reversed);
    console.log(password);
    ```
  * **Hasil Perhitungan (Password yang Digunakan):** **`bW9jLmxpYW1nQGhjaW5pbW1pay5ucmVvamI=`**

### 3\. Login ke Akun Bjoern

Kredensial yang dihitung (berdasarkan *write-up* terverifikasi) digunakan untuk *login*.

  * **Aksi:** Mengakses halaman *Login* Juice-Shop.

  * **Kredensial yang Berhasil Digunakan:**

      * **Email:** **`bjoern.kimminich@gmail.com`**
      * **Password:** **`bW9jLmxpYW1nQGhjaW5pbW1pay5ucmVvamI=`**

  * **Penyelesaian:** Dengan *login* yang berhasil menggunakan kombinasi Email/Password ini, *challenge* **Bjoern Login** selesai.

## Rekomendasi Keamanan

1.  **Gunakan *Hashing* Kuat:** Jangan pernah menyimpan atau menghitung *password* dengan cara yang dapat dibalik (*reversible*). Wajibkan penggunaan *hashing* yang kuat dan lambat (misalnya bcrypt, Argon2) dengan *salt* yang unik.
2.  **Server-Side Logic:** Logika pembuatan kata sandi (bahkan untuk *login* pihak ketiga) harus selalu diproses di sisi *server*, bukan di *client-side* JavaScript, untuk mencegah *reverse engineering* yang mudah.

## Bukti 

<img width="299" height="89" alt="Screenshot 2025-10-27 201837" src="https://github.com/user-attachments/assets/c505b611-5d91-4b69-b6be-570841176f67" />

<img width="218" height="106" alt="Screenshot 2025-10-27 201853" src="https://github.com/user-attachments/assets/d401ef55-9c3e-449f-816e-facfc4d65366" />
