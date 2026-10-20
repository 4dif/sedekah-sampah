# 🌱 SedekahSampah

> Platform pengelolaan sampah & minyak jelantah berbasis web — setor sampah, dapat poin, cairkan jadi rupiah. Ramah semua usia, mobile-first, installable sebagai aplikasi (PWA).

![Next.js](https://img.shields.io/badge/Next.js-14-black?logo=next.js)
![TypeScript](https://img.shields.io/badge/TypeScript-5.5-blue?logo=typescript)
![Prisma](https://img.shields.io/badge/Prisma-5.22-2D3748?logo=prisma)
![Tailwind](https://img.shields.io/badge/Tailwind-3.4-38BDF8?logo=tailwindcss)
![Playwright](https://img.shields.io/badge/Playwright-E2E-2EAD33?logo=playwright)
![License](https://img.shields.io/badge/license-MIT-green)

---

## 📑 Daftar Isi

1. [Fitur Utama](#-fitur-utama)
2. [Peran Pengguna](#-peran-pengguna)
3. [Arsitektur & Stack](#-arsitektur--stack)
4. [Prasyarat](#-prasyarat)
5. [Setup Lokal (5 Menit)](#-setup-lokal-5-menit)
6. [Akun Demo](#-akun-demo)
7. [Tutorial Penggunaan per Role](#-tutorial-penggunaan-per-role)
8. [Testing (Playwright E2E)](#-testing-playwright-e2e)
9. [PWA — Install di HP](#-pwa--install-di-hp)
10. [Deploy Production](#-deploy-production)
11. [Struktur Direktori](#-struktur-direktori)
12. [Troubleshooting](#-troubleshooting)
13. [Kontribusi & Lisensi](#-kontribusi--lisensi)

---

## ✨ Fitur Utama

- **Ajukan penjemputan sampah** (plastik, kertas, kaleng) — dari rumah, tanpa antre.
- **Setor minyak jelantah** dengan satuan liter.
- **Poin & rupiah otomatis** — setiap setoran diverifikasi jadi saldo.
- **Tiga role** dengan dashboard terpisah: **Warga**, **Petugas**, **Admin**.
- **Auto-assign petugas** — admin bisa tunjuk manual atau biarkan sistem memilih petugas dengan beban paling sedikit.
- **Upload foto bukti timbangan** (5MB, JPEG/PNG/WebP).
- **PWA installable** — bisa dipasang di HP Android/iOS seperti aplikasi native.
- **Offline-first** — halaman yang pernah dibuka tetap tampil saat internet putus.
- **Ramah lansia** — tombol minimum 44×44 px, teks ≥16 px, label ikon jelas.
- **Aksesibilitas**: `lang="id"`, label terasosiasi, kontras tinggi, ketuk-dan-baca jelas.
- **Laporan CSV** — unduh rekap transaksi per periode.
- **E2E test suite** — 7 spec Playwright untuk seluruh flow.

---

## 👥 Peran Pengguna

| Role | Hak Akses |
|---|---|
| **Warga** | Ajukan penjemputan, setor jelantah, lihat riwayat, lihat saldo poin |
| **Petugas** | Lihat tugas, telepon warga, input berat aktual, foto bukti |
| **Admin** | Dashboard KPI, assign petugas, verifikasi setoran, kelola harga, kelola pengguna, unduh laporan |

---

## 🏗 Arsitektur & Stack

- **Framework**: Next.js 14 (App Router) + TypeScript
- **Database**: PostgreSQL (via Prisma ORM)
- **Autentikasi**: NextAuth.js (credentials provider, JWT)
- **UI**: Tailwind CSS 3.4, komponen kustom
- **Validasi**: Zod (client & server)
- **Hashing**: bcryptjs
- **Test**: Playwright (Chromium + Pixel 7)
- **PWA**: Manifest + Service Worker kustom

```
┌─────────────┐      ┌──────────────┐      ┌──────────────┐
│  Browser    │─────▶│  Next.js API │─────▶│  PostgreSQL  │
│  (React)    │◀─────│  (RBAC + Zod)│◀─────│  (Prisma)    │
└─────────────┘      └──────────────┘      └──────────────┘
       │
       ├── Service Worker (offline cache)
       └── PWA Manifest (install)
```

---

## 📋 Prasyarat

- **Node.js ≥ 20** — cek dengan `node -v`
- **npm ≥ 10** (bundled dengan Node 20) atau pnpm/yarn
- **PostgreSQL ≥ 14** — bisa lokal, Docker, atau cloud (Supabase/Neon gratis)
- **Git** — untuk versioning & push ke GitHub
- (Opsional) **Playwright** untuk E2E test

**Instal PostgreSQL lewat Docker (paling cepat):**
```bash
docker run -d --name spg \
  -p 5432:5432 \
  -e POSTGRES_PASSWORD=pass \
  -e POSTGRES_DB=sedekah_sampah \
  postgres:16
```

---

## 🚀 Setup Lokal (5 Menit)

### 1. Clone project
```bash
git clone https://github.com/USERNAME/sedekah-sampah.git
cd sedekah-sampah
```

Atau jika belum ada repo GitHub (lihat [Bagian 11](#-deploy-production) untuk push pertama).

### 2. Install dependencies
```bash
npm install
```

### 3. Siapkan environment
```bash
cp .env.example .env
```

Edit `.env`:
```env
DATABASE_URL="postgresql://postgres:pass@localhost:5432/sedekah_sampah"
NEXTAUTH_SECRET="<generate-dengan-openssl-rand-base64-32>"
NEXTAUTH_URL="http://localhost:3000"
```

Generate `NEXTAUTH_SECRET`:
```bash
# Linux/Mac
openssl rand -base64 32

# Windows PowerShell
[Convert]::ToBase64String((1..32 | % { Get-Random -Max 256 }))
```

### 4. Migrasi database & seed
```bash
npx prisma migrate dev --name init
npm run db:seed
```

Output yang diharapkan:
```
✅ Seed selesai
```

### 5. Jalankan development server
```bash
npm run dev
```

Buka http://localhost:3000 → otomatis redirect ke `/masuk`.

### 6. (Opsional) Buka Prisma Studio
```bash
npx prisma studio
```

Buka http://localhost:5555 untuk melihat/edit data langsung.

---

## 🔑 Akun Demo

| Role | No. HP | Password | Redirect |
|---|---|---|---|
| Warga | `081200000003` | `warga123` | `/beranda` |
| Petugas | `081200000002` | `petugas123` | `/petugas` |
| Admin | `081200000001` | `admin123` | `/admin` |

> ⚠️ **Ganti password di production.** Akun demo hanya untuk development.

---

## 📖 Tutorial Penggunaan per Role

### 🟢 Warga — Ajukan Penjemputan Sampah

1. **Login** dengan akun warga → masuk ke **Beranda**.
2. Lihat **saldo poin** di kartu hijau besar.
3. Tap tombol **♻️ Jemput Sampah**.
4. **Pilih jenis sampah** — Plastik / Kertas / Kaleng.
5. **Atur berat** pakai tombol **+** / **−** (kelipatan 0.5 kg).
6. **Pilih tanggal & jam** penjemputan (minimal besok).
7. (Opsional) isi catatan — mis. "di pagar depan".
8. Tap **Ajukan Penjemputan** → status jadi **Menunggu**.

### 🟢 Warga — Setor Minyak Jelantah

1. Di Beranda, tap **🛢️ Setor Jelantah**.
2. Pilih **Minyak Jelantah**.
3. Isi perkiraan **liter** (mis. 1.5 L).
4. Atur jadwal → **Ajukan**.

### 🟢 Warga — Lacak & Riwayat

- Beranda menampilkan **penjemputan aktif** (status: Menunggu → Dijadwalkan → Dijemput → Ditimbang → Selesai).
- Tab **Riwayat** menampilkan seluruh penjemputan + poin yang didapat.
- Poin masuk ke saldo **setelah admin verifikasi**.

---

### 🔵 Petugas — Kerjakan Tugas

1. **Login** dengan akun petugas.
2. Di **Tugas Hari Ini** muncul daftar penjemputan yang di-assign ke Anda.
3. Setiap kartu menampilkan: nama warga, alamat, jenis sampah, estimasi berat.
4. Tap **📞 Telepon** untuk hubungi warga.
5. Tap **⚖️ Timbang** saat tiba di lokasi.
6. **Input berat aktual** pakai keypad besar (angka 0–9, tombol `.` untuk desimal, `C` untuk reset).
7. Tap **📷 Ambil / pilih foto** untuk bukti timbangan (dari kamera HP).
8. Tap **✓ Selesai Timbang** → tugas hilang dari daftar, status jadi **Ditimbang**.
9. Tab **Riwayat** menampilkan tugas yang sudah selesai.

---

### 🟠 Admin — Assign Petugas

1. **Login** sebagai admin.
2. Beranda admin menampilkan notifikasi kuning jika ada penjemputan **belum dijadwalkan**.
3. Tap notifikasi → masuk ke **Verifikasi & Assign**.
4. Bagian atas: **Perlu Dijadwalkan** — daftar penjemputan PENDING.
5. Pilih salah satu:
   - **👤 Pilih Petugas** → modal bottom sheet muncul, tap nama petugas.
   - **⚡ Auto** → sistem pilih petugas dengan beban tugas paling sedikit.

### 🟠 Admin — Verifikasi Setoran

1. Di halaman **Verifikasi**, scroll ke bagian **Siap Diverifikasi**.
2. Setiap kartu menampilkan: nama warga, jenis, estimasi vs aktual, total rupiah, foto bukti.
3. Periksa kecocokan foto & angka.
4. Tap **✓ Verifikasi & Cairkan Poin** → sistem otomatis:
   - Membuat transaksi.
   - Menambah poin warga.
   - Mengubah status pickup jadi **Selesai**.

### 🟠 Admin — Ubah Harga

1. Tab **Harga** di bottom nav.
2. Setiap jenis sampah punya input **Rp per kg/liter**.
3. Ubah angka → tap **Simpan** → konfirmasi "Harga diperbarui".

> **Catatan:** perubahan harga hanya berlaku untuk transaksi **baru**. Transaksi lama tetap pakai harga saat itu.

### 🟠 Admin — Unduh Laporan

1. Di **Dashboard Admin**, tap **⬇️ Unduh Laporan CSV**.
2. File `laporan-YYYY-MM-DD.csv` terunduh — bisa dibuka di Excel/Google Sheets.
3. Filter by periode: `GET /api/reports?from=2025-01-01&to=2025-01-31`.

---

## 🧪 Testing (Playwright E2E)

### Instalasi (sekali)
```bash
npm i -D @playwright/test
npx playwright install chromium
```

### Jalankan test
```bash
# Semua test, 2 project (desktop + mobile)
npm run test:e2e

# Hanya desktop
npx playwright test --project=chromium

# Mode UI interaktif (debug)
npm run test:e2e:ui

# Lihat report HTML
npm run test:e2e:report
```

### Yang diuji
| File | Cakupan |
|---|---|
| `01-auth.spec.ts` | Login/logout 3 role, RBAC redirect |
| `02-warga-ajukan.spec.ts` | Ajukan jemput + setor jelantah |
| `03-flow-lengkap.spec.ts` | **Full cycle**: warga → admin assign → petugas timbang → admin verifikasi → poin bertambah |
| `04-auto-assign.spec.ts` | Auto-assign petugas |
| `05-admin-harga.spec.ts` | Ubah harga + unduh CSV |
| `06-pwa.spec.ts` | Manifest, SW, offline fallback |
| `07-a11y.spec.ts` | Label, touch target ≥44px, `lang=id` |

### Reset data sebelum test (opsional)
```bash
curl -X POST http://localhost:3000/api/__test__/reset  # hanya jika E2E_RESET=1
```

---

## 📱 PWA — Install di HP

### Android (Chrome/Edge)
1. Buka `https://domain-anda.com` di Chrome.
2. Banner **"Pasang SedekahSampah"** muncul di atas bottom nav.
3. Tap **Pasang** → ikon muncul di home screen.
4. Buka dari ikon → tampil fullscreen tanpa address bar.

### iOS (Safari)
1. Buka di Safari.
2. Tap tombol **Share** (kotak dengan panah ke atas).
3. Pilih **Tambah ke Layar Utama**.
4. Tap **Tambah** → ikon muncul di home screen.

> **iPhone pengguna lansia:** buatkan shortcut di home screen dan ajari sekali — setelah itu tinggal tap ikon seperti aplikasi biasa.

### Uji offline
1. Buka app sekali saat online → cache terisi.
2. Matikan WiFi/data.
3. Buka app lagi → halaman yang pernah dibuka tetap tampil.
4. Halaman baru → tampil `/offline.html` dengan tombol "Coba Lagi".

---

## 🚢 Deploy Production

### Opsi A — Vercel (paling mudah, gratis untuk hobby)

1. Push ke GitHub (lihat langkah di bawah).
2. Buka [vercel.com/new](https://vercel.com/new), import repo.
3. Set environment variables:
   ```
   DATABASE_URL=<postgres cloud, mis. Neon>
   NEXTAUTH_SECRET=<openssl rand -base64 32>
   NEXTAUTH_URL=https://nama-app.vercel.app
   ```
4. **Penting:** ganti upload handler `/api/upload` dengan **Vercel Blob**:
   ```bash
   npm i @vercel/blob
   ```
   ```ts
   // src/app/api/upload/route.ts
   import { put } from "@vercel/blob";
   const { url } = await put(name, file, { access: "public" });
   return NextResponse.json({ url });
   ```
   Tambah `BLOB_READ_WRITE_TOKEN` di Vercel.
5. Deploy → otomatis migrasi DB via `prisma migrate deploy` (tambahkan ke build command: `prisma migrate deploy && next build`).

### Opsi B — VPS (Node + Nginx)

```bash
# Di server
git clone https://github.com/USERNAME/sedekah-sampah.git
cd sedekah-sampah
npm ci
cp .env.example .env  # isi DATABASE_URL, NEXTAUTH_SECRET, NEXTAUTH_URL
npx prisma migrate deploy
npm run build
npm start -- -p 3000
```

Gunakan **PM2**:
```bash
npm i -g pm2
pm2 start "npm start" --name sedekah-sampah
pm2 save && pm2 startup
```

Reverse proxy Nginx (`/etc/nginx/sites-available/sedekah-sampah`):
```nginx
server {
  server_name sedekah-sampah.id;
  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```
```bash
sudo ln -s /etc/nginx/sites-available/sedekah-sampah /etc/nginx/sites-enabled/
sudo certbot --nginx -d sedekah-sampah.id
sudo systemctl reload nginx
```

### Opsi C — Railway / Render / Fly.io
Semua mendukung Next.js + PostgreSQL langsung. Ikuti wizard masing-masing, set `DATABASE_URL` + `NEXTAUTH_SECRET`.

---

## 🐙 Cara Menyambungkan ke GitHub

### Langkah 1 — Siapkan repository

**Opsi A — Buat repo baru dari GitHub UI (rekomendasi):**
1. Buka https://github.com/new
2. Nama: `sedekah-sampah`
3. Visibility: **Private** (atau Public jika open-source)
4. **Jangan centang** "Add a README" (kita sudah punya).
5. Klik **Create repository**.
6. Salin URL HTTPS, mis. `https://github.com/USERNAME/sedekah-sampah.git`.

**Opsi B — Buat repo via GitHub CLI:**
```bash
# Install gh dulu: https://cli.github.com/
gh auth login
gh repo create sedekah-sampah --private --source=. --remote=origin --push
```
Jika pakai `gh repo create`, Anda bisa langsung skip ke **Langkah 5**.

### Langkah 2 — Inisialisasi Git di folder project

```bash
cd sedekah-sampah
git init
git add .
git commit -m "chore: initial commit — SedekahSampah MVP"
```

### Langkah 3 — Pastikan `.gitignore` benar

Isi `.gitignore` minimal:
```gitignore
node_modules/
.next/
dist/
.env
.env.local
*.log
playwright-report/
test-results/
public/uploads/*
!public/uploads/.gitkeep
.DS_Store
```

Buat file `.gitkeep`:
```bash
mkdir -p public/uploads
touch public/uploads/.gitkeep
```

### Langkah 4 — Hubungkan remote & push

```bash
git remote add origin https://github.com/USERNAME/sedekah-sampah.git
git branch -M main
git push -u origin main
```

**Jika muncul error 403 (auth):** GitHub tidak lagi menerima password. Gunakan **Personal Access Token**:

1. Buka https://github.com/settings/tokens
2. **Generate new token (classic)** → centang scope `repo` + `workflow`
3. Copy token (hanya tampil sekali).
4. Saat `git push` minta password → paste token sebagai password.

**Atau pakai SSH (lebih aman):**
```bash
# Generate SSH key (sekali seumur hidup per mesin)
ssh-keygen -t ed25519 -C "email@anda.com" -f ~/.ssh/id_ed25519 -N ""

# Copy public key
cat ~/.ssh/id_ed25519.pub
```
1. Buka https://github.com/settings/keys → **New SSH key** → paste → save.
2. Ubah remote:
```bash
git remote set-url origin git@github.com:USERNAME/sedekah-sampah.git
git push -u origin main
```

### Langkah 5 — Verifikasi

```bash
git remote -v
# origin  https://github.com/USERNAME/sedekah-sampah.git (fetch)
# origin  https://github.com/USERNAME/sedekah-sampah.git (push)

git status
# On branch main
# nothing to commit, working tree clean
```

Buka https://github.com/USERNAME/sedekah-sampah — semua file sudah tampil.

### Langkah 6 — Workflow tim selanjutnya

**Untuk kontributor baru** (setelah repo ada di GitHub):
```bash
git clone https://github.com/USERNAME/sedekah-sampah.git
cd sedekah-sampah
npm install
cp .env.example .env
# edit .env dengan DATABASE_URL lokal masing-masing
npx prisma migrate dev
npm run db:seed
npm run dev
```

**Buat fitur baru:**
```bash
git checkout -b feat/grafik-mingguan
# ... coding ...
git add .
git commit -m "feat(admin): grafik mingguan recharts"
git push -u origin feat/grafik-mingguan
```
Lalu buka Pull Request di GitHub → review → merge.

**Sinkronisasi dengan main:**
```bash
git checkout main
git pull
git checkout feat/nama-fitur
git rebase main
```

**Konvensi commit** (Conventional Commits):
- `feat:` fitur baru
- `fix:` perbaikan bug
- `docs:` dokumentasi
- `refactor:` refactoring tanpa ubah behavior
- `test:` test
- `chore:` setup, dependency, dll.

### Langkah 7 — CI/CD otomatis (opsional)

Sudah disiapkan `.github/workflows/e2e.yml` — jalankan test setiap push/PR. Set secret `DATABASE_URL` di **Settings → Secrets and variables → Actions** jika pakai DB eksternal.

Tambahkan juga `.github/workflows/deploy.yml` (opsional untuk auto-deploy Vercel):
```yaml
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Vercel
        run: npx vercel --token=${{ secrets.VERCEL_TOKEN }} --prod
        env:
          VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
          VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
```

---

## 📂 Struktur Direktori

```
sedekah-sampah/
├── .env.example
├── .gitignore
├── README.md
├── next.config.mjs
├── package.json
├── playwright.config.ts
├── postcss.config.mjs
├── tailwind.config.ts
├── tsconfig.json
├── prisma/
│   ├── schema.prisma
│   └── seed.ts
├── public/
│   ├── manifest.webmanifest
│   ├── sw.js
│   ├── offline.html
│   ├── icons/
│   │   ├── icon-192.png
│   │   └── icon-512.png
│   └── uploads/
│       └── .gitkeep
├── src/
│   ├── middleware.ts             # RBAC guard
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── globals.css
│   │   ├── masuk/page.tsx
│   │   ├── (warga)/
│   │   │   ├── layout.tsx
│   │   │   ├── beranda/page.tsx
│   │   │   └── warga/...
│   │   ├── petugas/
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx
│   │   │   ├── timbang/[id]/page.tsx
│   │   │   ├── riwayat/page.tsx
│   │   │   └── profil/page.tsx
│   │   ├── admin/
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx
│   │   │   ├── verifikasi/
│   │   │   │   ├── page.tsx
│   │   │   │   └── AssignSection.tsx
│   │   │   ├── pengguna/page.tsx
│   │   │   └── harga/page.tsx
│   │   └── api/
│   │       ├── auth/[...nextauth]/route.ts
│   │       ├── pickups/route.ts
│   │       ├── pickups/[id]/weigh/route.ts
│   │       ├── pickups/[id]/verify/route.ts
│   │       ├── pickups/[id]/assign/route.ts
│   │       ├── petugas/route.ts
│   │       ├── wastetypes/[id]/route.ts
│   │       ├── upload/route.ts
│   │       ├── dashboard/stats/route.ts
│   │       ├── reports/route.ts
│   │       └── __test__/reset/route.ts
│   ├── components/
│   │   ├── Providers.tsx
│   │   ├── BigActionButton.tsx
│   │   ├── StatCard.tsx
│   │   ├── StatusBadge.tsx
│   │   ├── AjukanForm.tsx
│   │   ├── TimbangForm.tsx
│   │   ├── VerifikasiCard.tsx
│   │   ├── HargaEditor.tsx
│   │   ├── AssignModal.tsx
│   │   ├── InstallPrompt.tsx
│   │   ├── ServiceWorkerRegister.tsx
│   │   └── SignOutButton.tsx
│   ├── lib/
│   │   ├── auth.ts
│   │   ├── prisma.ts
│   │   ├── rbac.ts
│   │   └── ratelimit.ts
│   └── types/
│       └── next-auth.d.ts
└── tests/
    └── e2e/
        ├── helpers.ts
        ├── global-setup.ts
        └── 0X-*.spec.ts
```

---

## 🔧 Troubleshooting

### `PrismaClientInitializationError: Can't reach database`
- Cek PostgreSQL hidup: `pg_isready -h localhost -p 5432`
- Cek `DATABASE_URL` di `.env` sesuai kredensial.

### `NEXTAUTH_SECRET` missing
- Pastikan `.env` ada di root project (bukan di `src/`).
- Restart `npm run dev` setelah ubah `.env`.

### Login berhasil tapi redirect balik ke `/masuk`
- Cek `NEXTAUTH_URL` di `.env` harus sama dengan URL akses (`http://localhost:3000`).
- Hapus cookie: DevTools → Application → Cookies → hapus semua.

### Service Worker tidak aktif
- SW **hanya aktif di production**. Jalankan `npm run build && npm start`, bukan `npm run dev`.
- DevTools → Application → Service Workers → klik **Unregister** jika ada versi lama.

### Upload foto gagal di Vercel
- Vercel tidak punya disk persisten → wajib ganti ke Vercel Blob (lihat [Deploy Production](#-deploy-production)).

### `npm run test:e2e` gagal "server not ready"
- Cek port 3100 tidak dipakai.
- Build manual dulu: `npm run build`, lalu jalankan `npm start -- -p 3100` di terminal lain, set `E2E_BASE_URL=http://localhost:3100 npm run test:e2e`.

### Playwright test flaky di CI
- Tambah `await page.waitForLoadState("networkidle")` setelah navigasi.
- Naikkan `expect.timeout` di `playwright.config.ts` jadi 10_000.

### Warga tidak bisa lihat poin bertambah
- Poin hanya bertambah **setelah admin verifikasi**. Cek status pickup di Prisma Studio → `WEIGHED` berarti belum diverifikasi.

---

## 🤝 Kontribusi & Lisensi

### Branch strategy
- `main` — production-ready.
- `feat/*` — fitur baru, PR ke `main`.
- `fix/*` — bug fix, PR ke `main`.

### Standar kode
- TypeScript strict mode.
- Commit Conventional Commits.
- Formatter: Prettier (`npx prettier --write .`).
- Linter: `npm run lint`.

### Roadmap
- [ ] Notifikasi WhatsApp (Fonnte/Twilio)
- [ ] Grafik mingguan di dashboard admin (Recharts)
- [ ] Dark mode + toggle font lansia
- [ ] Peta rute petugas (Leaflet + OpenStreetMap)
- [ ] Riwayat pencairan rupiah (transfer bank)
- [ ] Multi-tenant (per RT/RW)

### Lisensi
MIT © 2025 — bebas digunakan, dimodifikasi, dan didistribusikan dengan atribusi.

---

## 📞 Kontak & Dukungan

- **Issue tracker**: https://github.com/USERNAME/sedekah-sampah/issues
- **Diskusi**: https://github.com/USERNAME/sedekah-sampah/discussions
- **Email**: tim@sedekah-sampah.id (ganti dengan kontak Anda)

---

**Dibuat dengan 🌱 untuk lingkungan yang lebih bersih.**