# Project-RPC-System-
# SECTION 1 - Project Overview 

**Apa yang dibangun:** Website pendaftaran event lari yang digunakan untuk mengelola proses peserta mulai dari registrasi hingga pengambilan racepack.

**Masalah yang diselesaikan:** Menyelesaikan permasalahan penyelenggara yang kesulitan untuk melakukan dan mengidentifikasi identitas, dan nomor pendaftaran peserta secara manual dalam proses pemberian race pack. Web ini menyelesaikan kerumitan pencatatan manual di kertas/spreadsheet, mencegah kecurangan data ganda, serta mengatasi risiko salah serah terima ukuran jersey dan nomor BIB melalui verifikasi kode terpusat. 

**Menyediakan apa:** Website ini menyediakan sistem terpusat untuk kelola pendaftaran dan pengidentifikasian identitas peserta, sebagai berikut:

**1) Bagi Peserta (Di Sisi Depan / Front-End):**
Web ini menyediakan layanan pendaftaran mandiri secara online. Peserta bisa melihat informasi lengkap event (jadwal, kategori 5K/10K, dan benefit), mengisi formulir pendaftaran yang aman (tervalidasi NIK dan usia), melakukan pembayaran langsung, hingga tampil konfirmasi pendaftaran digital yang berisi nomor BIB, kode unik, dan status aktif mereka.

**2) Bagi Panitia (Di Sisi Dasbor / Back-End):**
Web ini menyediakan alat manajemen operasional. Panitia dibekali fitur untuk memasukkan kode pendaftaran peserta guna mencocokkan data jersey dan nomor BIB saat serah terima (handover), serta halaman rekapitulasi data yang menampilkan total peserta aktif dan sisa racepack yang belum diambil secara real-time.


# SECTION 2 - User personas

**Penyelenggara Event**

**Tujuan:** Mengelola proses pendaftaran peserta event lari secara mandiri dan terintegrasi melalui sistem online agar proses administrasi menjadi lebih efektif dan terorganisir.

**Pain Point:** Selama ini proses pendaftaran dan pendataan peserta masih dilakukan secara manual menggunakan formulir, kertas, atau spreadsheet secara manual menggunakan formulir, kertas, atau spreadsheet sehingga berisiko menyebabkan duplikasi data peserta, kesalahan pencatatan identitas, serta kesalahan saat proses serah terima racepack dan BIB. 

**Kebutuhan Utama:** Membutuhkan sistem terpusat yang dapat digunakan untuk mengelola pendaftaran peserta, menyimpan identitas pelari, melakukan proses handover racepack, serta menghasilkan rekapitulasi peserta secara otomatis.

**Peserta (Calon Pelari)**

**Tujuan:** Memudahkan proses pendaftaran event lari secara online tanpa harus melakukan pencatatan dan registrasi secara manual

**Pain Point:** Proses pendaftaran manual dinilai berpotensi terjadi kesalahan data peserta, serta berisiko menyebabkan kesalahan saat pengambilan racepack dan BIB. 

**Kebutuhan Utama:** Membutuhkan sistem pendaftaran yang mudah digunakan untuk melakukan registrasi peserta, mengisi identitas pelari, melakukan pembayaran, memperoleh informasi nomor BIB dan kode pendaftaran, serta mempermudah proses pengambilan racepack tanpa kesalahan.


# SECTION 3 - Tech Stack
Backend            : Laravel 12
Frontend           : Blade + Tailwind CSS4
Database           : My SQL
Authentication     : Laravel Breeze
Dashboard UI       : Filament PHP 
Development Tools  : VS Code, Composer, XAMPP

# SECTION 4 — Data Models

**1. Admins**
Digunakan untuk menyimpan data panitia atau penyelenggara yang memiliki akses ke dashboard pengelolaan peserta dan racepack. 
- Id
- Name
- Email
- Password
- Role: enum ('admin', 'staff')
- Phone_number
- Timestamps
  
**2. Events**
Digunakan untuk menyimpan informasi event lari yang diselenggarakan.
-  Id
- Event_name
- Slug
- Description
- Event_date
- Location
- Registration_open
- Registration_close
- Poster
- Timestamps
  
**3. Participants**
Digunakan untuk menyimpan seluruh data peserta yang melakukan pendaftaran event.
Keterangan:
- BIB_number digunakan sebagai nomor peserta saat event berlangsung
- registration_code digunakan untuk pengambilan racepack
- participant_status menunjukkan status peserta setelah pembayaran

- Id
- event_id (FK: events)
- Full_name
- Bib_name
- Email
- Phone_number
- Birth_date
- NIK (one participants)
- Emergency_contact
- Medical_history (nullable)
- Jersey_size: enum('S', 'M', 'L', 'XL')
- Category: enum('5K', '10K')
- BIB_number (unique)
- Registration_code (unique)
- Participant_status: enum('pending', 'active', 'inactive')
- Timestamps
  
**4. Payments**
Digunakan untuk menyimpan data pembayaran peserta.
Keterangan:
a. Status awal peserta adalah pending
b. Setelah pembayaran diverifikasi, status berubah menjadi paid
c. Peserta kemudian menjadi active

- Id
- Participant_id (FK: participants)
- Invoice_number (unique)
- Amount
- Payment_method
- Payment_proof
- Payment_status: enum('pending', 'paid', 'failed')
- Payment_date (nullable)
- Verified_by (FK: admins)
- Timestamps
  
**5. Racepacks**
Digunakan untuk mengelola proses serah terima BIB dan racepack.
Keterangan:
a. Panitia memasukkan registration_code
b. Sistem menampilkan data peserta
c. Status berubah dari not_taken menjadi taken

- Id
- Participant_id (FK: participants)
- BIB_number
- Jersey_size
- Pickup_status: enum('not_taken', 'taken')
- Pickup_time (nullable)
- Handover_by (FK: admins)
- Timestamps
  
**6. Reports**
Digunakan untuk menampilkan rekapitulasi peserta dan status pengambilan racepack pada dashboard panitia.
Data rekap dihasilkan dari proses perhitungan data pada tabel participants dan racepacks.

- Total_participants
- Total_5k
- Total_10k
- Total_taken_racepack
- Total_not_taken_racepack
- Report_date
- Timestamps

**7. Relasi**
- Admin → mengelola data peserta, pembayaran, dan proses handover racepack
- Event → memiliki banyak Participants
- Participant → memiliki satu Payment
- Participant → memiliki satu Racepack
- Participant → memiliki banyak Notifications
- Racepack → memiliki banyak Handover Transactions
- Racepack → diverifikasi oleh Admin
- Payment → diverifikasi oleh Admin

# Section 5 - Core Features
**Event Information Page**
Peserta dapat melihat informasi lengkap event seperti kategori lari, jadwal, lokasi, benefit peserta, dan detail racepack sebelum melakukan pendaftaran.

**Online Registration System**
Peserta dapat mengisi formulir pendaftaran secara online dengan validasi otomatis:
1. Minimal usia 12 tahun.
2. 1 NIK hanya untuk 1 peserta.
3. Pemilihan kategori 5K atau 10K.
4. Pemilihan ukuran jersey.

Sistem otomatis membuat:
1. Nomor BIB
2. Kode registrasi peserta

**Payment & Registration Confirmation**
Setelah registrasi, peserta diarahkan ke halaman pembayaran. Setelah pembayaran berhasil diverifikasi, peserta memperoleh:

1. Status peserta aktif
2. Nomor BIB
3. Kode pendaftaran
4. Bukti registrasi yang dapat diunduh

**Racepack Handover Dashboard**
Panitia dapat memasukkan kode registrasi peserta untuk memverifikasi data pengambilan racepack.
Sistem menampilkan:
1. Nama peserta
2. Nama BIB
3. Kategori lari
4. Ukuran jersey
5. Status pengambilan
Panitia dapat mengubah status menjadi “Taken” setelah racepack diserahkan.

**Participant Recap Dashboard**
Dashboard menampilkan data rekapitulasi secara real-time:
1. Total peserta
2. Total kategori 5K dan 10K
3. Total racepack sudah diambil
4. Total racepack belum diambil
5. Total peserta aktif

# SECTION 6 - User Flows 
**Participant Registration Flow**
1. Peserta membuka homepage.
2. Peserta melihat informasi event.
3. Peserta klik tombol daftar.
4. Sistem menampilkan formulir registrasi.
5. Peserta mengisi data diri.
6. Sistem memvalidasi usia dan NIK.
7. Sistem membuat nomor BIB dan kode registrasi.
8. Peserta diarahkan ke pembayaran.
9. Setelah pembayaran berhasil, status peserta menjadi aktif.

**Racepack Handover Flow**
1. Panitia login ke dashboard.
2. Panitia membuka menu handover.
3. Panitia memasukkan kode registrasi.
4. Sistem menampilkan data peserta.
5. Panitia menyerahkan racepack.
6. Sistem mengubah status menjadi “Taken”.

**Participant Recap Flow**
1. Panitia membuka dashboard rekap.
2. Sistem menghitung data peserta secara otomatis.
3. Dashboard menampilkan total peserta dan status racepack secara real-time.

# SECTION 7 - Out of Scope
(sistem pendaftaran dan pengambilan racepack, bukan sistem pelacakan atau hasil lomba)
Fitur berikut belum termasuk dalam versi pertama sistem:
1. Integrasi Payment Gateway Otomatis
Verifikasi pembayaran masih dilakukan secara manual oleh admin (upload bukti transfer), bukan otomatis melalui gateway pembayaran seperti Midtrans, Xendit, atau Stripe.

2. Notifikasi Otomatis (Email & WhatsApp)
Sistem belum mengirimkan email atau pesan WhatsApp otomatis setelah registrasi, pembayaran berhasil, atau pengingat pengambilan racepack.

3. Sistem Manajemen Multi-Event
Website hanya mendukung satu event lari dalam satu waktu. Untuk menyelenggarakan event baru, perlu dilakukan instalasi ulang atau modifikasi data manual pada database.

4. Aplikasi Mobile (Android/iOS)
Sistem hanya tersedia dalam bentuk website responsif (desktop/mobile browser), bukan aplikasi native.

5. QR Code Check-in
Proses verifikasi pengambilan racepack hanya menggunakan input kode registrasi manual, bukan scan QR code.

6. Live Race Tracking (GPS)
Tidak ada fitur pelacakan posisi peserta secara real-time saat lomba berlangsung.

7. Leaderboard & Hasil Lomba Online
Sistem tidak menampilkan papan peringkat atau waktu finis peserta secara online setelah event selesai.

8. Export Data Peserta dalam Format Lanjutan
Belum tersedia fitur export data ke Excel/CSV dengan filter dinamis (hanya tersedia tampilan rekap di dashboard).

## SECTION 8 — Non-functional Requirements
### Non-functional Requirements
#### Security
* Semua akun peserta, admin, dan organizer wajib menggunakan sistem login yang aman
* Role-based authorization diterapkan sesuai hak akses pengguna
* Data peserta seperti nama, email, dan nomor telepon disimpan secara aman
* Seluruh form registrasi memiliki input validation untuk mencegah invalid data
* Pembayaran peserta menggunakan sistem payment gateway yang aman dan terenkripsi

#### Performance
* Website mampu menangani lonjakan traffic saat periode pendaftaran dibuka
* Proses registrasi dan pembayaran peserta berjalan secara real-time
* Database indexing diterapkan pada kolom:
  * participant_id
  * event_id
  * category_id
  * payment_status
* Halaman event dan form registrasi harus memiliki loading time yang cepat dan responsif

#### Reliability
* Sistem tetap stabil saat diakses banyak peserta secara bersamaan
* Data registrasi peserta tersimpan otomatis setelah pembayaran berhasil
* Sistem memiliki backup database untuk mengurangi risiko kehilangan data

#### Scalability
* Sistem dapat digunakan untuk penambahan event lari lain di masa mendatang
* Platform mampu menangani peningkatan jumlah peserta tanpa penurunan performa 

#### Code Quality
* Menggunakan clean architecture dan modular coding
* Business logic dipisahkan ke dalam Service Layer
* Tidak ada credential atau API key yang ditulis langsung di source code (menggunakan environment variables)
* Struktur kode mudah dikembangkan dan dipelihara untuk fitur event berikutnya
