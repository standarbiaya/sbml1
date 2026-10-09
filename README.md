# Dashboard SBML — versi siap pakai

Aplikasi untuk inventarisasi usulan **Standar Biaya Masukan Lainnya (SBML)**, berisi 9 kolom sesuai permintaan:

`No | Nama KL | Tahun | No Surat / Tgl | Perihal | Surat Menkeu / Tgl | Karakteristik K/L | Jenis SBML | Status`

## Cara A — LANGSUNG JALANKAN, tanpa instalasi

1. Ekstrak ZIP.
2. Klik dua kali `public/index.html` untuk membuka di Chrome atau Edge.
3. Dashboard langsung terbuka dengan **8 baris data demonstrasi fiktif**. Silakan hapus, ubah, tambah, atau impor file CSV/JSON dari data sendiri.
4. Klik `Tambah Usulan` untuk membuat baris baru; klik `Ubah`/`Hapus` di baris yang ada; data disimpan secara lokal di browser (`localStorage`). Gunakan `Ekspor CSV`/`JSON` sebagai cadangan. Penyimpanan hanya untuk browser dan perangkat yang sama; jangan gunakan mode incognito untuk data tetap.
5. Untuk impor, gunakan `contoh-data/template-sbml.csv` dan isi data Anda di sana. Header wajib memiliki `Nama KL` dan `Status`, dan disarankan mempertahankan kesembilan header.

**Aturan diagram**: Pie menampilkan hanya status **Disetujui** dan **Ditolak**. Status dalam proses, belum ada keputusan, atau status lain dihitung terpisah dan **tidak dimasukkan ke pie**. `Disetujui Sebagian` dikelompokkan sebagai disetujui; `Tidak Disetujui` sebagai ditolak. Grafik dan statistik hasil filter mengikuti pencarian, tahun, nama K/L, dan jenis SBML, sementara empat kartu KPI utama menampilkan total semua data.

## Cara B — Publikasi Cloudflare Workers

Prasyarat: Node.js, akun Cloudflare, internet.

1. Buka terminal/PowerShell di folder hasil ekstrak (sejajar `wrangler.jsonc`).
2. Jalankan `npx wrangler login` untuk login Cloudflare.
3. Jalankan `npx wrangler deploy`. URL `https://dashboard-sbml.<subdomain>.workers.dev` akan diberikan oleh Cloudflare. Ini sudah cukup untuk menggunakan dashboard di internet dalam mode data lokal; tidak perlu Google Sheets dahulu.

**Peringatan:** Data lokal tidak dibagikan lintas komputer/pengguna. Untuk penggunaan bersama, ikuti konfigurasi Google Sheets di bawah.

## Cara C — Hubungkan Google Sheets (baca saja)

1. Buat Google Sheets, tab bernama `SBML`; baris pertama wajib berisi header di atas persis dengan urutan A:I. Isi usulan mulai baris kedua.
2. Di Google Cloud Console, aktifkan **Google Sheets API**; buat **Service Account** dan unduh kunci JSON-nya secara aman.
3. Bagikan spreadsheet kepada email `client_email` service account dengan izin **Viewer**.
4. Di terminal, jalankan perintah ini satu per satu, lalu masukkan nilainya saat diminta:

   ```bash
   npx wrangler secret put SHEET_ID
   npx wrangler secret put GOOGLE_CLIENT_EMAIL
   npx wrangler secret put GOOGLE_PRIVATE_KEY
   ```

   `SHEET_ID` adalah bagian URL spreadsheet di antara `/d/` dan `/edit`. `GOOGLE_CLIENT_EMAIL` dan `GOOGLE_PRIVATE_KEY` diambil dari kunci JSON service account. Untuk private key, tempel nilai lengkap termasuk `BEGIN/END PRIVATE KEY` (line break nyata atau `\\n` sama-sama didukung).
5. Jalankan ulang `npx wrangler deploy`. Dashboard otomatis memuat data Sheet bila API siap. Tombol **Buka Sheet** dan **Data Lokal** akan muncul. `Refresh` membaca ulang Sheet; pengubahan data di dashboard dinonaktifkan dalam mode Sheet.

**Keamanan:** API Google Sheets pada paket ini tidak melakukan autentikasi pengguna, sehingga siapa pun yang mengetahui URL Worker bisa membaca data jika aplikasi di-deploy secara publik. Untuk data internal K/L dan surat resmi, **WAJIB** batasi akses website **dan endpoint `/api/sbml`** menggunakan Cloudflare Access / kebijakan organisasi. Jangan pernah memasukkan `GOOGLE_PRIVATE_KEY` ke HTML atau mengunggahnya ke repo publik.

## Fitur
- Diagram donat pie status disetujui vs ditolak, diperbarui otomatis mengikuti filter non-status.
- Kartu ringkasan total, disetujui, ditolak, dalam proses/status lain.
- Filter status via tombol cepat, filter tahun, K/L, jenis SBML, pencarian semua kolom.
- Matriks 9 kolom plus kolom aksi untuk edit/hapus lokal, halaman 20 baris.
- Impor CSV (koma atau titik koma, termasuk nilai dengan koma dan baris baru) atau JSON (array objek), ekspor CSV/JSON, cetak.
- Tanpa dependensi CDN (dapat berfungsi offline), responsif di desktop dan HP.
- Sinkronisasi baca-saja Google Sheets via Cloudflare Workers.

## Pemecahan masalah
- Dashboard terbuka tetapi Google Sheets belum terhubung: **normal**, mode lokal digunakan otomatis. Untuk Google Sheets, deploy terlebih dahulu dan isi secrets.
- Jika Sheet kosong atau error: pastikan tab `SBML`, header, hak akses service account, aktivasi API, serta secrets cocok.
- Setelah file CSV diimpor dan ingin kembali ke demo: hapus data lokal browser untuk situs itu, lalu refresh. Sebelum menghapus, ekspor data untuk cadangan.
- Jangan gunakan data fiktif dari paket ini sebagai dasar keputusan fiskal.
