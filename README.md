# Dashboard SBML

Dashboard usulan Standar Biaya Masukan Lainnya (SBML), dengan pencarian semua kolom, filter karakteristik K/L dan status, grafik donat, ekspor CSV, dan integrasi Google Sheets.

## Format Google Sheets
Buat tab bernama `SBML`. Baris pertama harus memiliki tepat sembilan header berikut (urutannya sesuai tampilan):

`No | Nama KL | Tahun | No Surat / Tgl | Perihal | Surat Menkeu / Tgl | Karakteristik K/L | Jenis SBML | Status`

Setiap baris setelahnya merupakan satu usulan/surat. **Data contoh dalam aplikasi sepenuhnya fiktif.**

## Coba lokal
Buka `public/index.html` di browser. Jika API tidak tersedia, dashboard menggunakan data demonstrasi. Grafik menggunakan Chart.js dari CDN sehingga membutuhkan koneksi internet.

## Deploy Cloudflare Workers
1. Install Node.js. Di folder proyek, jalankan `npx wrangler login`.
2. Aktifkan Google Sheets API pada Google Cloud, buat service account, dan dapatkan kunci JSON.
3. Bagikan Google Sheet ke alamat email service account dengan izin Viewer.
4. Jalankan `npx wrangler secret put SHEET_ID`, masukkan ID spreadsheet.
5. Jalankan `npx wrangler secret put GOOGLE_CLIENT_EMAIL`, masukkan client_email dari JSON.
6. Jalankan `npx wrangler secret put GOOGLE_PRIVATE_KEY`, masukkan private_key dari JSON.
7. Jalankan `npx wrangler deploy`.

Tab/range default adalah `SBML!A:I`. Jangan memasukkan kunci privat ke HTML atau repositori. Endpoint API tidak memiliki autentikasi pengguna; untuk data surat internal, lindungi situs beserta endpoint API dengan Cloudflare Access sebelum dipublikasikan.
