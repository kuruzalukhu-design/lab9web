# Lab9Web — Praktikum 9: PHP Modular

Repository praktikum **Pemrograman Web** topik **PHP Modular**: modularisasi program,
function, template, dan routing sederhana, lalu diterapkan pada aplikasi CRUD berbasis database.

> Laporan lengkap (analisis, jawaban pertanyaan, screenshot, kendala) ada di [`docs/LAPORAN.md`](docs/LAPORAN.md).

## 1. Tujuan Project
1. Memahami modularisasi program dan manfaatnya.
2. Membedakan `include`, `require`, `include_once`, `require_once`.
3. Membuat template (header/footer) yang dipakai bersama.
4. Membuat function PHP (parameter, return, type declaration, nilai default) dan memisahkannya ke file helper.
5. Menyusun struktur direktori aplikasi PHP yang modular.
6. Menerapkan routing berbasis parameter URL dengan whitelist dan halaman 404.
7. Mengintegrasikan semuanya ke aplikasi database (CRUD pengguna) — `latihan4-crud`.

## 2. Kebutuhan Perangkat
| Kebutuhan | Keterangan |
|---|---|
| Web server lokal | XAMPP / Laragon (Apache + MySQL/MariaDB) |
| PHP | 8.0 atau lebih baru (ekstensi `pdo_mysql`, `mbstring`) |
| Browser | Chrome / Firefox / Edge |
| Editor | Visual Studio Code |
| Git | untuk commit & push repository |
| (Opsional) `mod_rewrite` | untuk URL ringkas via `.htaccess` |

## 3. Cara Menjalankan
1. Salin/clone folder `Lab9Web` ke docroot web server  
   (XAMPP: `C:\xampp\htdocs\Lab9Web`, Laragon: `C:\laragon\www\Lab9Web`).
2. Jalankan **Apache** dan **MySQL**.
3. Latihan non-database (`01` s.d. `latihan3`) langsung dibuka di browser, contoh:
   - `http://localhost/Lab9Web/01-include-require/uji-include.php`
   - `http://localhost/Lab9Web/02-template/home.php`
   - `http://localhost/Lab9Web/03-function/contoh-function.php`
   - `http://localhost/Lab9Web/latihan1-template/home.php`
   - `http://localhost/Lab9Web/latihan2-function/index.php`
   - `http://localhost/Lab9Web/latihan3-router/index.php`
4. Untuk **latihan4-crud**:
   1. Buka phpMyAdmin → tab *Import* → pilih `latihan4-crud/sql/db_latihan.sql` → Go
      (membuat database `db_latihan`, tabel `users`, dan 3 akun contoh).
   2. Sesuaikan kredensial di `latihan4-crud/config/database.php` bila perlu
      (default: host `localhost`, user `root`, password kosong).
   3. Buka `http://localhost/Lab9Web/latihan4-crud/index.php`.
   4. Login: **admin@example.com** / **admin123**.

## 4. Struktur Direktori
```
Lab9Web/
├── README.md
├── .gitignore
├── docs/
│   ├── LAPORAN.md              # laporan praktikum
│   └── screenshots/            # tangkapan layar
├── 01-include-require/         # praktik include vs require
├── 02-template/                # template header/footer (home, about, kontak)
├── 03-function/                # contoh function + helpers.php + latihan function
├── latihan1-template/          # Latihan 1: Home, Profil, Kontak
├── latihan2-function/          # Latihan 2: helpers/functions.php (4 function)
├── latihan3-router/            # Latihan 3: router aman, 5 rute + 404
└── latihan4-crud/              # Latihan 4: refactoring CRUD (aplikasi utama)
    ├── index.php               # router (whitelist)
    ├── .htaccess               # URL rewrite (opsional)
    ├── config/database.php     # koneksi PDO (satu-satunya)
    ├── helpers/functions.php   # e(), url(), flash, CSRF, validasi, dll.
    ├── views/                  # header, footer, dashboard, 404
    ├── modules/
    │   ├── auth/               # login, logout
    │   └── user/               # list, add, edit, delete
    ├── assets/                 # css/, js/
    └── sql/db_latihan.sql
```

## 5. Daftar Rute (`latihan4-crud`)
| Rute (`?page=`) | File | Login | Keterangan |
|---|---|---|---|
| `dashboard` (default) | `views/dashboard.php` | ya | Ringkasan data |
| `login` | `modules/auth/login.php` | tidak | Form login |
| `logout` | `modules/auth/logout.php` | tidak | Logout (hanya POST) |
| `user/list` | `modules/user/list.php` | ya | Daftar pengguna |
| `user/add` | `modules/user/add.php` | ya | Tambah pengguna |
| `user/edit` | `modules/user/edit.php` | ya | Ubah pengguna (`&id=`) |
| `user/delete` | `modules/user/delete.php` | ya | Hapus pengguna (hanya POST + CSRF) |
| *lainnya* | `views/404.php` | – | Status HTTP 404 |

Rute `latihan3-router`: `home`, `about`, `produk`, `kontak`, `faq` (+ 404).

Dengan `.htaccess` aktif, `/Lab9Web/latihan4-crud/user/list` diteruskan ke `index.php?page=user/list`.

## 6. Langkah Praktikum
1. **Include vs require** — `01-include-require/`: bandingkan perilaku saat file tidak ditemukan.
2. **Template** — `02-template/`: `header.php`, `footer.php`, `home.php`, `about.php`, `kontak.php`, `style.css`.
3. **Function** — `03-function/`: function tanpa/dengan parameter, return, type declaration; pindahkan ke `helpers.php`; latihan Celcius→Fahrenheit, predikat nilai, total belanja (dipakai di 2 halaman).
4. **Latihan 1** — `latihan1-template/`: Home, Profil, Kontak dengan template yang sama.
5. **Latihan 2** — `latihan2-function/`: `formatRupiah()`, `hitungDiskon()`, `validasiEmail()`, `ringkasTeks()` dipakai di 2 halaman.
6. **Latihan 3** — `latihan3-router/`: router whitelist 5 rute + 404.
7. **Latihan 4** — `latihan4-crud/`: refactor CRUD ke struktur modular + router + template bersama.
8. **Tantangan mandiri** (semua dikerjakan di `latihan4-crud`):
   - menu aktif (`is_active()` di `helpers/functions.php`, dipakai `views/header.php`);
   - helper pesan sukses/gagal (`flash_set()` / `flash_render()`);
   - pembatasan akses login (`'auth' => true` pada tabel rute, sesi login);
   - URL rewrite `.htaccess` (lihat bagian 5 dan `docs/LAPORAN.md`).

## 7. Catatan Keamanan yang Diterapkan
- Router memakai **whitelist** — input `page` tidak pernah disusun menjadi path file.
- Seluruh output di-escape dengan `e()` (`htmlspecialchars`).
- Query memakai **prepared statement** (PDO).
- Tambah/ubah/hapus lewat **POST** + **token CSRF**; hapus via URL (GET) ditolak (405).
- Password disimpan dengan `password_hash()` dan diverifikasi dengan `password_verify()`.
- Detail error koneksi database hanya masuk ke log, tidak ditampilkan ke pengguna.
