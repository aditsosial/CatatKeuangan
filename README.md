# Buku Kas

Aplikasi web untuk mencatat pemasukan dan pengeluaran, dipisah per kategori, dengan rekap per kategori dan per bulan. Database menggunakan **Google Sheet**, backend menggunakan **Google Apps Script**, frontend adalah file statis (`index.html`) yang bisa di-hosting di mana saja (termasuk GitHub Pages).

## Arsitektur

```
index.html  ->  fetch()  ->  Apps Script Web App  ->  Google Sheet ("Transaksi")
```

Semua pengguna yang membuka `index.html` akan membaca & menulis ke Google Sheet yang sama melalui Apps Script.

## Langkah 1 — Siapkan Google Sheet

1. Buka sheets.google.com, buat spreadsheet baru, beri nama misalnya **Buku Kas DB**.
2. Tidak perlu membuat sheet/kolom manual — skrip di langkah 2 akan otomatis membuat sheet bernama `Transaksi` dengan header yang benar saat pertama kali dipanggil.

## Langkah 2 — Pasang Apps Script

1. Di spreadsheet tadi, buka **Extensions → Apps Script**.
2. Hapus kode default di `Code.gs`, lalu salin-tempel seluruh isi file **`Code.gs`** yang disertakan di proyek ini.
3. Klik **Save** (ikon disket), beri nama project misalnya "Buku Kas API".

## Langkah 3 — Deploy sebagai Web App

1. Di Apps Script editor, klik **Deploy → New deployment**.
2. Klik ikon gear di samping "Select type", pilih **Web app**.
3. Isi:
   - **Execute as**: `Me (email Anda)`
   - **Who has access**: `Anyone` (agar bisa diakses dari browser tanpa login Google)
4. Klik **Deploy**. Google akan meminta otorisasi izin — setujui (klik **Advanced → Go to [nama project] (unsafe)** jika muncul peringatan, ini normal untuk script milik sendiri).
5. Salin **Web app URL** yang muncul (formatnya `https://script.google.com/macros/s/XXXXX/exec`).

## Langkah 4 — Hubungkan ke index.html

1. Buka `index.html`, cari baris:
   ```js
   const APPS_SCRIPT_URL = 'PASTE_URL_WEB_APP_DI_SINI';
   ```
2. Ganti dengan URL Web App dari Langkah 3, contoh:
   ```js
   const APPS_SCRIPT_URL = 'https://script.google.com/macros/s/XXXXX/exec';
   ```
3. Simpan file. Buka `index.html` di browser — aplikasi sudah terhubung ke Google Sheet Anda.

## Menjalankan secara lokal

```bash
python3 -m http.server 8000
# buka http://localhost:8000
```

## Deploy ke GitHub Pages

1. Buat repo baru di GitHub, push folder ini:
   ```bash
   git init
   git add .
   git commit -m "Initial commit: buku kas dengan Google Sheet backend"
   git branch -M main
   git remote add origin https://github.com/USERNAME/buku-kas.git
   git push -u origin main
   ```
2. Buka **Settings → Pages** di repo, pilih branch `main`, folder root, simpan.
3. Situs aktif di `https://USERNAME.github.io/buku-kas/`.

> Catatan: `APPS_SCRIPT_URL` akan terlihat oleh siapa pun yang melihat source code halaman ini (karena berjalan di browser). Ini normal untuk arsitektur seperti ini — pastikan Apps Script Anda tidak melakukan aksi sensitif, dan pertimbangkan menambahkan validasi sederhana di `doPost` (misalnya token rahasia) jika ingin lebih aman.

## Update kode Apps Script di kemudian hari

Setiap kali mengubah isi `Code.gs`, Anda perlu membuat deployment baru agar perubahan aktif:
**Deploy → Manage deployments → klik ikon pensil pada deployment aktif → Version: New version → Deploy.**
URL Web App biasanya tetap sama, jadi tidak perlu mengubah `APPS_SCRIPT_URL` di `index.html`.

## Struktur proyek

```
buku-kas/
├── index.html   # frontend aplikasi (HTML, CSS, JS)
├── Code.gs      # backend Google Apps Script (salin ke Apps Script editor)
└── README.md
```

## Kolom di Google Sheet ("Transaksi")

| id | tipe | kategori | jumlah | tanggal | catatan |
|----|------|----------|--------|---------|---------|
| string unik | pemasukan / pengeluaran | nama kategori | angka | YYYY-MM-DD | teks bebas |

Anda bisa membuka & memeriksa data langsung di Google Sheet kapan saja — ini juga berguna sebagai backup manual.
