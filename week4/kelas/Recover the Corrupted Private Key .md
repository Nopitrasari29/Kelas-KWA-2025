# Write-up: Recover the Corrupted Private Key (Kunci RSA Terpotong)

## 1\. Tujuan dan Analisis Awal

Tantangan ini mengharuskan pemulihan kunci privat RSA yang disimpan dalam format PEM (`id_rsa`) yang tidak lengkap atau terkorupsi. Kunci yang diberikan terpotong di bagian akhir, menyebabkan kegagalan *parsing* ASN.1 (Abstract Syntax Notation One) oleh *tool* kriptografi standar.

### Analisis File Kunci:

1.  **Format:** Kunci menggunakan format **PEM** (Base64).
2.  **Pemotongan:** Kunci tidak memiliki tag penutup (`-----END RSA PRIVATE KEY-----`).
3.  **Kecurigaan Kriptografi:** Data Base64 terakhir (`VewhljCGq0mSZR5bGqTfDcuXaHvDuvQE9G1NECm3vV1klF5nmgQA5ZMoFTrw1Xrg`) memiliki panjang 78 karakter, yang **bukan kelipatan 4**. Ini menunjukkan *padding* Base64 hilang.

## 2\. Proses Pemulihan dan Koreksi Format

Untuk memulihkan kunci, kita memerlukan *tool* kriptografi standar, yaitu **OpenSSL**, yang harus diakses melalui Terminal (PowerShell/WSL).

### Step 1: Instalasi Tool (Pre-requisite)

  * Di Windows, OpenSSL diinstal menggunakan *package manager* seperti `winget` (misalnya: `winget install OpenSSL.OpenSSL`) atau melalui lingkungan Linux (WSL).

### Step 2: Koreksi Format Dasar (Tag Penutup)

Pertama, file kunci harus dikoreksi dengan menambahkan tag penutup yang hilang:

```
# Kunci yang Terkorupsi
...VewhljCGq0mSZR5bGqTfDcuXaHvDuvQE9G1NECm3vV1klF5nmgQA5ZMoFTrw1Xrg
-----END RSA PRIVATE KEY-----
```

### Step 3: Koreksi Padding Base64 yang Hilang

Setelah menambahkan tag penutup, verifikasi dengan `openssl rsa -in corrupted.key -check` menunjukkan kegagalan *parsing* karena *padding* yang hilang.

  * Panjang data terakhir: 78 karakter.
  * Kebutuhan *padding* (agar kelipatan 4): 80 - 78 = 2 karakter.

Kunci dikoreksi dengan menambahkan **dua tanda sama dengan (`==`)** di akhir data Base64:

```
# Kunci yang Dikoreksi dengan Padding
...VewhljCGq0mSZR5bGqTfDcuXaHvDuvQE9G1NECm3vV1klF5nmgQA5ZMoFTrw1Xrg== 
-----END RSA PRIVATE KEY-----
```

### Step 4: Normalisasi Format (Satu Baris)

Karena kegagalan *parsing* terus berlanjut (meskipun *padding* sudah benar), format kunci dinormalisasi menjadi satu baris data Base64 panjang (menghapus *line wrapping*), untuk menghindari *error* *parsing* ASN.1 yang sensitif terhadap *whitespace* atau *line break* yang tidak perlu.

## 3\. Kesimpulan Kriptografi

Meskipun semua perbaikan format (tag penutup, padding `==`, dan normalisasi format) telah diterapkan, OpenSSL masih mengembalikan *error* **`Could not find private key from corrupted.key`**.

Hal ini mengarah pada kesimpulan bahwa:

  * **Kegagalan ASN.1:** Data kunci itu sendiri (salah satu komponen integer: Modulus, Prime 2, atau Coefficient) telah rusak atau hilang di bagian tengah data Base64 (bukan hanya di bagian *padding*).
  * **Kunci Tidak Dapat Dipulihkan:** Karena OpenSSL tidak dapat mengurai struktur data utama (ASN.1), kunci tersebut dianggap rusak secara fundamental dan tidak dapat dipulihkan hanya dengan teknik koreksi format sederhana.
