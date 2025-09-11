# Juice Shop Write-up: Web3 Sandbox Challenge

## Challenge Overview

**Judul:** Web3 Sandbox

**Kategori:** Broken Access Control

**Kesulitan:** ⭐ (1/6)

Tantangan ini bertujuan untuk menemukan sebuah sandbox kode Web3 yang tidak sengaja dibiarkan terekspos. Tantangan ini mengeksplorasi konsep Broken Access Control dalam aplikasi web, khususnya pada lingkungan yang melibatkan teknologi Web3.

## Tools Used
*   Peramban web (Web browser)

## Metodologi dan Solusi

Proses untuk menyelesaikan tantangan ini sangatlah lugas:

1.  **Penemuan URL Tersembunyi:** Kami memulai dengan menjelajahi URL yang mungkin terkait dengan fitur Web3, dengan asumsi bahwa sandbox mungkin terletak di jalur yang tidak umum atau tersembunyi.

2.  **Menebak URL:** Menggunakan pendekatan sederhana dengan mencoba berbagai nama direktori umum yang sering digunakan untuk lingkungan pengujian atau pengembangan, seperti `/sandbox` atau `/test`.

3.  **Mengakses Sandbox:** Setelah mencoba berbagai variasi, kami menemukan URL yang benar: `http://127.0.0.1:3000/#/web3-sandbox`. Mengakses URL ini secara langsung berhasil membuka antarmuka sandbox kode Web3 yang sepenuhnya fungsional.

### Penjelasan Solusi

Tantangan ini berhasil dieksploitasi karena kelalaian keamanan yang umum, yaitu membiarkan fitur pengembangan atau eksperimen tetap dapat diakses di lingkungan produksi. Akses langsung ke URL tersebut memberikan akses tanpa batas ke fungsionalitas yang seharusnya dilindungi, yang merupakan risiko keamanan signifikan.

### Bukti 
<img width="1919" height="1079" alt="Screenshot 2025-09-11 104107" src="https://github.com/user-attachments/assets/3c927891-a6e1-43c8-bdca-1c795fc680ba" />
