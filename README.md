# Website SMK Infokom — Versi Laravel + MySQL (XAMPP)

Ini adalah hasil konversi dari project **"WPS Ress Ukk"** (HTML/CSS/JS statis dengan
data di `localStorage`) menjadi aplikasi **Laravel 10** dengan database **MySQL**,
siap dijalankan di **XAMPP** dan dikelola lewat **phpMyAdmin**.

---

## 🆕 Apa yang berubah dari versi lama?

| Versi Lama (statis) | Versi Baru (Laravel) |
|---|---|
| Data tersimpan di `localStorage` browser (hilang jika ganti device/browser/clear cache) | Data tersimpan permanen di **database MySQL** |
| Login admin **hardcoded** `admin` / `admin123` langsung di kode JavaScript (bisa dilihat siapa saja lewat "Inspect Element") | Login memakai sistem **autentikasi Laravel** dengan password **ter-enkripsi (hash)** di database |
| Form Kontak hanya menampilkan `alert()` palsu, pesan **tidak pernah tersimpan** | Pesan kontak benar-benar **tersimpan ke database** dan bisa dibaca admin di menu "Pesan Masuk" |
| Form PPDB tidak divalidasi sama sekali | Ada **validasi lengkap** di server (semua field wajib diisi, format tanggal/email diperiksa) |
| Logo di halaman login admin memakai path lokal Windows (`C:\Users\HP\Downloads\...`) → gambar **selalu gagal tampil** di komputer lain | Path gambar diperbaiki memakai `asset()` Laravel, tampil di komputer manapun |
| Upload gambar tidak ada (hanya bisa isi URL manual) | Sekarang admin bisa **upload file gambar langsung** dari komputer (tersimpan di `storage/`), atau tetap bisa isi URL |
| Data jurusan, statistik beranda, info kontak sekolah **di-hardcode** di banyak file HTML sekaligus | Sekarang jadi satu sumber data (**tabel `settings`**) yang bisa diubah dari menu **Pengaturan** di admin panel |
| Tidak ada halaman "Pesan Masuk" untuk admin | Ditambahkan menu **Pesan Masuk** + halaman detail pesan |
| Data pendaftar PPDB tidak bisa diubah statusnya | Ditambahkan fitur **ubah status** (Pending / Diterima / Ditolak) langsung dari tabel |
| Pendaftar tidak punya cara mengecek statusnya sendiri | Ditambahkan **kode pendaftaran** ala kode transaksi (mis. `PPDB-2610-00001`) + halaman **Cek Status PPDB** — cukup masukkan kode untuk melihat Diterima / Pending / Tidak Diterima tanpa membuka data siswa lain |
| Belum ada menu Kesiswaan (OSIS, ekskul, prestasi, dll) | Ditambahkan menu **Kesiswaan** lengkap (lihat bawah) |
| Sidebar admin penuh emoji (📊📰👥) | Diganti dengan **ikon SVG** bergaya konsisten (seperti di halaman login) |
| Gambar hasil upload kadang tidak tampil ("broken image") karena path tersimpan tidak cocok dengan alamat akses aplikasi | Cara penyimpanan & penampilan gambar diperbaiki total (lihat bawah) + ada **preview langsung** saat memilih file/URL di form admin |

### 🆕 Fitur Kesiswaan (baru)

Menu **"Kesiswaan"** kini muncul di navbar publik (dropdown) dan di sidebar admin, mencakup:
- **OSIS & MPK** — struktur pengurus, jabatan, kelas, periode
- **Ekstrakurikuler** — daftar ekskul, jadwal, pembina
- **Prestasi Siswa** — daftar prestasi per siswa/tim, tingkat lomba, kategori
- **Kegiatan Siswa** — dokumentasi kegiatan yang diselenggarakan OSIS/MPK/sekolah
- **Beasiswa** — daftar program beasiswa, persyaratan, kuota, batas waktu
- **Tata Tertib** — kategori aturan sekolah beserta daftar poinnya

Semua bisa dikelola penuh (tambah/edit/hapus) lewat panel admin.

### 🖼️ Perbaikan sistem gambar (upload & preview)

Sebelumnya gambar hasil upload admin kadang **tidak muncul / error**, karena path yang
disimpan ke database tidak selalu cocok dengan alamat (domain/port/subfolder) yang
dipakai untuk mengakses aplikasi. Ini sudah diperbaiki:

- Path gambar hasil upload disimpan **relatif** ke database, lalu dirender lewat helper
  `img_url()` yang otomatis menyesuaikan ke URL yang benar apa pun cara Anda mengakses
  aplikasi (`php artisan serve`, XAMPP di subfolder, dll).
- Semua form admin yang punya field gambar sekarang menampilkan **preview langsung**
  begitu Anda memilih file atau mengetik URL — jadi Anda langsung tahu jika gambar
  gagal dimuat, tanpa perlu submit dulu.
- Setelah upload gambar baru, gambar lama (jika hasil upload, bukan URL luar) otomatis
  dihapus dari server supaya tidak menumpuk file yang tidak terpakai.

Kalau gambar tetap tidak muncul setelah update ini, pastikan Anda sudah menjalankan
`php artisan storage:link` (lihat langkah instalasi di bawah).

---

## 📋 Kebutuhan Sistem

- **XAMPP** (PHP ≥ 8.1, MySQL/MariaDB) — [xampp.apache.org](https://www.apachefriends.org/)
- **Composer** — [getcomposer.org](https://getcomposer.org/download/) (untuk menginstal library Laravel)
- Browser modern

> ⚠️ Folder project ini **belum** berisi folder `vendor/` (library Laravel).
> Folder tersebut otomatis dibuat saat Anda menjalankan `composer install` pada
> langkah 3 di bawah — wajib terhubung internet saat langkah itu saja.

---

## 🚀 Langkah Instalasi

### 1. Salin project ke folder XAMPP

Ekstrak/salin seluruh folder ini ke dalam folder `htdocs` XAMPP, misalnya:

```
C:\xampp\htdocs\wps-ress-ukk\
```

### 2. Nyalakan Apache & MySQL

Buka **XAMPP Control Panel**, klik **Start** pada modul **Apache** dan **MySQL**.

### 3. Install dependency Laravel via Composer

Buka **Command Prompt / Terminal**, arahkan ke folder project, lalu jalankan:

```bash
cd C:\xampp\htdocs\wps-ress-ukk
composer install
```

Tunggu sampai proses selesai (mengunduh Laravel Framework dan library pendukung).

### 4. Buat file `.env`

Salin file `.env.example` menjadi `.env`:

```bash
copy .env.example .env
```
*(di Linux/Mac: `cp .env.example .env`)*

Lalu generate application key:

```bash
php artisan key:generate
```

### 5. Buat database lewat phpMyAdmin

1. Buka browser, akses **http://localhost/phpmyadmin**
2. Klik tab **Databases**
3. Buat database baru dengan nama: **`smk_infokom`** (collation `utf8mb4_unicode_ci`)
4. Klik **Create** — biarkan kosong (tabel akan dibuat otomatis lewat migration di langkah 6)

### 6. Sesuaikan koneksi database

Buka file `.env`, pastikan bagian berikut sesuai dengan setting MySQL XAMPP Anda
(secara default XAMPP: user `root`, password kosong):

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=smk_infokom
DB_USERNAME=root
DB_PASSWORD=
```

### 7. Jalankan migration + seeder (buat tabel & isi data awal)

```bash
php artisan migrate --seed
```

Perintah ini akan otomatis:
- Membuat semua tabel (`news`, `teachers`, `galleries`, `agendas`, `ppdb_registrants`,
  `contact_messages`, `majors`, `settings`, `users`, dll) di database `smk_infokom`
- Mengisi data awal (berita, guru, galeri, agenda, jurusan) **persis seperti data
  contoh di versi lama**
- Membuat 1 akun admin default

### 8. Buat symbolic link untuk file upload

```bash
php artisan storage:link
```

Ini diperlukan agar gambar yang di-upload admin (berita, foto guru, galeri) bisa
tampil di browser.

### 9. Jalankan aplikasi

**Opsi A — pakai Apache bawaan XAMPP** (disarankan, karena project sudah ada di `htdocs`):

Akses langsung: **http://localhost/wps-ress-ukk/public**

*(Jika ingin URL tanpa `/public`, buat Virtual Host mengarah ke folder `public/`,
atau gunakan Opsi B di bawah)*

**Opsi B — pakai server bawaan Laravel** (lebih mudah untuk development):

```bash
php artisan serve
```

Lalu buka: **http://localhost:8000**

---

## 🔑 Login Admin

Buka **`/admin/login`** (mis. `http://localhost:8000/admin/login`), lalu masuk dengan:

```
Username : admin
Password : admin123
```

> ⚠️ **Segera ganti password ini** setelah login pertama kali untuk keamanan.
> (Fitur "ubah password sendiri" belum ada di menu admin — untuk saat ini bisa
> diubah langsung lewat phpMyAdmin pada tabel `users`, kolom `password`, dengan
> nilai hash baru dari `php artisan tinker` → `Hash::make('password_baru')`.)

---

## 🗂️ Struktur Fitur

### Halaman Publik
- **Beranda** (`/`) — hero slider, statistik, berita terbaru, galeri, CTA PPDB
- **Profil** (`/profil`) — sejarah, visi-misi, sarana-prasarana, akreditasi
- **Jurusan** (`/jurusan`) — daftar 3 program keahlian (RPL, TKJ, DKV)
- **Guru & Staff** (`/guru`) — dikelompokkan: pimpinan, guru, staff kependidikan
- **Kesiswaan** (dropdown menu):
  - **OSIS & MPK** (`/kesiswaan/osis-mpk`) — struktur pengurus
  - **Ekstrakurikuler** (`/kesiswaan/ekstrakurikuler`) — daftar & jadwal ekskul
  - **Prestasi Siswa** (`/kesiswaan/prestasi-siswa`) — daftar prestasi siswa/tim
  - **Kegiatan Siswa** (`/kesiswaan/kegiatan-siswa`) — dokumentasi kegiatan
  - **Beasiswa** (`/kesiswaan/beasiswa`) — daftar program beasiswa
  - **Tata Tertib** (`/kesiswaan/tata-tertib`) — peraturan sekolah per kategori
- **Berita** (`/berita`, `/berita/{slug}`) — daftar & detail berita/artikel
- **Galeri** (`/galeri`) — foto kegiatan & fasilitas, dengan lightbox
- **Agenda** (`/agenda`) — jadwal kegiatan sekolah
- **Kontak** (`/kontak`) — info kontak + form pesan (tersimpan ke database)
- **PPDB** (`/ppdb`) — form pendaftaran online (tersimpan ke database)

### Panel Admin (`/admin/...`, wajib login)
- **Dashboard** — ringkasan statistik & data terbaru
- **Kelola Berita** — tambah/edit/hapus, upload gambar
- **Kelola Guru & Staff** — tambah/edit/hapus, upload foto
- **Kelola Galeri** — tambah/edit/hapus foto
- **Kelola Agenda** — tambah/edit/hapus jadwal
- **Kelola Jurusan** — edit deskripsi, kompetensi, gambar tiap jurusan
- **Kesiswaan**:
  - **OSIS & MPK** — kelola anggota, jabatan, organisasi, periode
  - **Ekstrakurikuler** — kelola daftar ekskul, jadwal, pembina
  - **Prestasi Siswa** — kelola daftar prestasi
  - **Kegiatan Siswa** — kelola dokumentasi kegiatan
  - **Beasiswa** — kelola program beasiswa, status buka/tutup
  - **Tata Tertib** — kelola kategori & poin aturan sekolah
- **Data PPDB** — lihat pendaftar + kode pendaftaran (bisa disalin), ubah status (pending/diterima/ditolak), hapus
- **Cek Status PPDB** (`/ppdb/cek-status`) — pendaftar mengecek statusnya sendiri hanya dengan kode pendaftaran
- **Pesan Masuk** — baca pesan dari form kontak
- **Pengaturan** — ubah nama sekolah, alamat, telepon, email, statistik beranda, media sosial

---

## 🛠️ Troubleshooting

**"SQLSTATE[HY000] [1049] Unknown database 'smk_infokom'"**
→ Anda belum membuat database di phpMyAdmin (lihat langkah 5).

**Gambar hasil upload admin tidak muncul (ikon rusak)**
→ Jalankan `php artisan storage:link` (langkah 8), pastikan folder
`storage/app/public` dan `public/storage` (symlink) ada.

**Halaman blank / error 500**
→ Cek `storage/logs/laravel.log` untuk detail error. Pastikan `.env` sudah ada
dan `php artisan key:generate` sudah dijalankan.

**"could not find driver" saat migrate**
→ Aktifkan extension `pdo_mysql` di `php.ini` XAMPP (`extension=pdo_mysql`,
`extension=mysqli`), lalu restart Apache.

**Ingin reset semua data ke kondisi awal**
```bash
php artisan migrate:fresh --seed
```
⚠️ Perintah ini akan **menghapus semua data** dan mengisi ulang dengan data contoh.

---

## 📁 Struktur Folder Penting

```
app/
  Http/Controllers/          → Controller halaman publik
  Http/Controllers/Admin/    → Controller panel admin
  Models/                    → Model Eloquent (News, Teacher, Gallery, dll)
database/
  migrations/                → Struktur tabel database
  seeders/                   → Data awal (sama seperti data.js versi lama)
resources/views/
  layouts/                   → Layout utama (app.blade.php, admin.blade.php)
  pages/                     → Halaman publik
  admin/                     → Halaman panel admin
  partials/, components/     → Navbar, footer, header halaman
public/
  css/, js/, images/         → Asset asli dari versi statis (tetap dipakai)
routes/web.php               → Semua rute/URL aplikasi
```

Selamat menggunakan! 🎓
