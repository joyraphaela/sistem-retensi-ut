# Sistem Retensi Mahasiswa Universitas Terbuka (UT)
Aplikasi Pemantauan Kemajuan Studi dan Early Warning System Berbasis Client-Side untuk UT Daerah Papua.

## 1. Pendahuluan
Sistem Retensi Mahasiswa UT adalah aplikasi berbasis web yang dirancang untuk mengotomatisasi deteksi dini terhadap mahasiswa yang berpotensi mengalami retensi atau putus studi (drop-out). Sistem ini memproses data distribusi grade (D, E, dan C-) dari file rekapitulasi nilai hasil ujian (format .xlsx) yang diterbitkan oleh pusat, kemudian mengklasifikasikannya ke dalam empat zonasi prioritas intervensi secara instan.

Proyek ini dibangun untuk menyelesaikan kendala efisiensi operasional pada UT Daerah (seperti UT Jayapura/UT Sorong) yang sebelumnya mengolah puluhan ribu baris data secara manual menggunakan spreadsheet konvensional.

## 2. Arsitektur & Fitur Utama
Sistem dirancang dengan arsitektur **Offline-First** untuk memastikan performa maksimal pada spesifikasi perangkat keras operasional standar kantor daerah:

- **High-Performance Client-Side Parsing:** Mampu melakukan parsing dan memproses lebih dari 16.000 data baris nilai dalam hitungan detik langsung di browser pengguna menggunakan pustaka SheetJS.
- **Asynchronous Data Processing:** Memanfaatkan Web Workers API untuk memisahkan komputasi berat dari main thread browser, mencegah terjadinya pembekuan antarmuka (UI freezing).
- **Persistent Local Database (Offline-First):** Terintegrasi dengan Dexie.js (IndexedDB wrapper) untuk menyimpan hasil kalkulasi dan metadata dashboard di penyimpanan lokal browser. Data tetap aman dari proses penyegaran halaman (refresh) tanpa membutuhkan infrastruktur server database eksternal.
- **Interactive Visual Dashboard:** Visualisasi data distribusi grade menggunakan grafik batang dan komposisi kelompok mahasiswa menggunakan grafik donat berbasis Chart.js.
- **Data Pagination & Virtualization:** Optimasi rendering tabel HTML untuk menjaga stabilitas memori laptop operasional staf lapangan.
- **Automated Academic Alert System:** Fitur pratinjau pesan otomatis yang terintegrasi secara modular untuk pengiriman notifikasi pembimbingan akademik via WhatsApp.

## 3. Spesifikasi Teknologi (Tech Stack)
Aplikasi ini dikembangkan secara modular menggunakan komponen inti sebagai berikut:
- **Core Engine:** Vanilla JavaScript (ES6+), HTML5, Cascading Style Sheets (Tailwind CSS via CDN)
- **Database Wrapper:** Dexie.js (IndexedDB API Wrapper)
- **Data Parser:** SheetJS (XLSX Parser)
- **Visual Rendering:** Chart.js (Data Visualization)

## 4. Spesifikasi Masukan Data (Data Input Schema)
Aplikasi menerima input file spreadsheet dengan ekstensi `.xlsx` atau `.csv` yang memuat nama kolom wajib (case-sensitive) berikut:

| Nama Kolom | Tipe Data | Deskripsi |
| :--- | :--- | :--- |
| `masa_ut` | String / Integer | Periode masa registrasi atau ujian mahasiswa |
| `nim` | String (Numeric) | Nomor Induk Mahasiswa (Primary Identifier) |
| `nama_mahasiswa` | String | Nama lengkap mahasiswa sesuai SIA |
| `kode_matakuliah` | String | Kode resmi mata kuliah |
| `nama_matakuliah` | String | Nama lengkap mata kuliah terkait |
| `grade` | Character | Huruf mutu evaluasi hasil belajar (A, A-, B, B-, C, C-, D, E) |

## 5. Parameter Klasifikasi Intervensi
Logika penentuan status kemajuan studi mahasiswa didasarkan pada akumulasi distribusi nilai kritis yang didapatkan setelah ujian akhir semester:

```javascript
if (nilai_e_dan_d_tinggi_proporsional) {
    status = "Butuh Intervensi"; // Prioritas utama tindakan lapangan
} else if (akumulasi_d_dan_e_signifikan) {
    status = "Perlu Pendampingan"; // Disarankan untuk konseling akademik
} else if (ada_beberapa_nilai_d_atau_c_minus) {
    status = "Dipantau"; // Pemantauan berkala
} else {
    status = "Baik"; // Kemajuan studi berjalan normal
}
```

## 6. Jaminan Keamanan & Privasi Data
Sistem ini mengadopsi prinsip Sandbox dan kebijakan Same-Origin Policy. Karena seluruh proses ekstraksi, kalkulasi, dan visualisasi data dilakukan sepenuhnya di dalam memori lokal browser (client-side), **tidak ada satu pun data mahasiswa yang dikirimkan ke jaringan internet eksternal atau server pihak ketiga**. Langkah ini menjamin kepatuhan penuh terhadap perlindungan data akademik internal Universitas Terbuka.
