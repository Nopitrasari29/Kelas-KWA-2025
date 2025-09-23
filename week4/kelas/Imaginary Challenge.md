# Juice-Shop Write-up: Imaginary Challenge

## 1. Tantangan

**Judul:** Imaginary Challenge
**Kategori:** Cryptographic Issues
**Tingkat Kesulitan:** ⭐⭐⭐⭐⭐⭐ (6/6)

Tantangan "Imaginary Challenge" di OWASP Juice Shop adalah salah satu yang paling rumit karena menuntut pemahaman mendalam tentang **mekanisme internal aplikasi**. Tujuannya adalah untuk "menyelesaikan" tantangan nomor 999 yang tidak ada dengan merekayasa balik **"Continue Code"** yang valid.

---

## 2. Metodologi dan Solusi

Pendekatan untuk tantangan ini adalah **analisis kode sumber** (white-box testing) karena metode konvensional (black-box testing) tidak berhasil.

### Langkah 1: Analisis Kode Sumber (Kunci Solusi)

Semua upaya awal untuk membuat hash yang valid gagal karena parameter yang digunakan tidak cocok dengan yang ada di server. Dengan menganalisis kode sumber, parameter rahasia yang digunakan oleh Juice Shop ditemukan:

*   **`salt`**: `'this is my salt'`
*   **`minLength`**: `60`
*   **`alphabet`**: `'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890'`

Parameter ini sangat berbeda dari yang umum digunakan dan menjadi akar masalah dari semua kegagalan sebelumnya.

### Langkah 2: Pembuatan `Continue Code` yang Benar

Dengan parameter yang ditemukan, skrip JavaScript berikut dibuat untuk menghasilkan `Continue Code` yang valid. Skrip ini dijalankan langsung di **Console browser** untuk menghindari masalah format data.

```javascript
// Definisikan parameter yang ditemukan dari analisis kode sumber
const salt = 'this is my salt';
const minLength = 60;
const customAlphabet = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890';

// Muat pustaka hashids dari CDN dan jalankan fungsi utama
const script = document.createElement('script');
script.src = 'https://cdnjs.cloudflare.com/ajax/libs/hashids/2.2.10/hashids.min.js'; // Memperbaiki URL CDN
script.onload = () => {
    // Buat hash dengan parameter yang benar
    const hashids = new Hashids(salt, minLength, customAlphabet);
    const continueCode = hashids.encode(999);
    console.log('Final Continue Code yang Benar adalah:', continueCode);
};
document.head.appendChild(script);
```

### Langkah 3: Mengirimkan `Continue Code` ke Server

Kode yang dihasilkan adalah string panjang seperti **`69OZrZ8ajEgXWnOzYd46VwKmGk6aE9t7k2rK63YpQQLpJn1b5v5LVdJ`**. Kode ini harus dikirim ke server melalui permintaan `PUT` dengan format JSON yang benar. Skrip berikut melakukan tugas ini.

```javascript
// Kode untuk mengirimkan Continue Code
const finalCode = '69OZrZ8ajEgXWnOzYd46VwKmGk6aE9t7k2rK63YpQQLpJn1b5v5LVdJ';
const url = 'http://localhost:3000/rest/continue-code/apply';

fetch(url, {
    method: 'PUT',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        continueCode: finalCode
    })
})
.then(response => response.json())
.then(data => {
    console.log('Respons Server:', data);
    if (data.challenge === 'Imaginary Challenge') {
        console.log('BERHASIL! Tantangan telah diselesaikan! 🎉');
    }
})
.catch(error => console.error('Terjadi kesalahan:', error));
```

---

## 3. Kesimpulan dan Pembelajaran

Tantangan ini menyoroti risiko **penggunaan nilai *default*** dalam konteks keamanan dan pentingnya **analisis kode** untuk menemukan celah yang tidak terlihat. Kegagalan berulang kali terjadi karena parameter *hashing* yang tidak terduga, yang hanya dapat ditemukan dengan memeriksa kode sumber. Tantangan ini adalah contoh utama dari **white-box penetration testing** dan mengajarkan bahwa keamanan tidak dapat bergantung pada kerahasiaan.

## Bukti 
<img width="1004" height="667" alt="Screenshot 2025-09-23 171225" src="https://github.com/user-attachments/assets/4252e731-4ab8-45f4-b65a-1d73e6efd589" />

<img width="661" height="390" alt="Screenshot 2025-09-23 172013" src="https://github.com/user-attachments/assets/f6900e6e-702b-4840-b046-25bd0bf8dc7b" />
