# Area Dimension Monitoring System (Supabase + Vercel)

Aplikasi statis satu halaman (tanpa Node/npm). Data di Supabase, hosting di Vercel.

Isi folder:
- `index.html` – aplikasi
- `config.js` – tempat mengisi URL dan anon key Supabase
- `denah-pabrik.png` – gambar denah Anda (diunggah lewat aplikasi)
- `supabase/01_migration.sql` – tabel, aturan keamanan (RLS), bucket Storage
- `supabase/02_seed_areas.sql` – 17 area dari tabel Anda
- `vercel.json`

## Urutan (penting)
### 1. Supabase
1. supabase.com → New project.
2. SQL Editor → New query → tempel isi `supabase/01_migration.sql` → Run.
3. Authentication → Providers: pastikan Email aktif (untuk uji awal, matikan "Confirm email").
4. Project Settings → API: salin **Project URL** dan **anon public key** (bukan service_role).

### 2. Isi config.js
Ganti dua nilai di `config.js` dengan URL dan anon key tadi.

### 3. Deploy ke Vercel
Cara tanpa Node:
1. Buat repo di github.com → Add file → Upload files → unggah SEMUA isi folder ini (`index.html` harus di root repo).
2. vercel.com → Add New → Project → pilih repo. Framework Preset: **Other**. Build Command dan Output Directory dibiarkan kosong. Deploy.
3. Supabase → Authentication → URL Configuration: isi **Site URL** dengan alamat Vercel Anda.

Cara dengan CLI (jika Node ada): `npx vercel` di folder ini.

### 4. Jadikan diri Anda admin
1. Buka situs Vercel → "Buat akun viewer".
2. Supabase SQL Editor:
```sql
update public.profiles set role='admin'
where id=(select id from auth.users where email='email@anda.com');
```
3. Logout, login lagi.

### 5. Unggah dan aktifkan denah
Settings → nama "Denah Pabrik" → pilih `denah-pabrik.png` → Unggah → **Aktifkan**.

### 6. Masukkan 17 area
SQL Editor → tempel `supabase/02_seed_areas.sql` → Run. Buka Interactive Floor Plan: pilih "RCF Area" untuk melihat kotak dan ukurannya (102 m × 24 m = 2.448 m²).

### 7. Rapikan posisi kotak
Kotak ID 1–7 dan 17 (RCF) mengikuti label di denah. ID 8–16 hanya **perkiraan**: buka Manage Areas, pilih area, lalu gambar ulang kotaknya di denah.

## Keamanan
- Viewer hanya bisa membaca; admin ditentukan oleh tabel `profiles` di database dan ditegakkan oleh RLS.
- Bucket `floor-plans` privat; gambar dimuat lewat signed URL.
- Jangan pernah memasukkan service_role key ke `config.js`.

## Masalah umum
- Halaman menampilkan "Isi SUPABASE_URL…": `config.js` belum diisi atau belum ikut ter-upload.
- "violates row-level security": akun belum admin.
- Seed menghasilkan 0 baris: denah belum diunggah.
- Gambar tidak muncul: pastikan 01_migration.sql dijalankan penuh.
