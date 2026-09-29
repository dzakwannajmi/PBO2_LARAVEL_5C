# Panduan Fork Repo Dosen (Windows & macOS)

Panduan ini menjelaskan cara mengambil repo dosen, mengerjakan tugas di repo milikmu sendiri, dan tetap bisa mengambil pembaruan dari repo dosen.

**Istilah singkat**

| Istilah | Arti |
|---|---|
| **Fork** | Salinan repo dosen di akun GitHub-mu. Kamu bebas mengubahnya tanpa memengaruhi repo dosen. |
| **Clone** | Mengunduh repo fork-mu ke laptop. |
| **`origin`** | Repo fork milikmu (tempat kamu `push`). |
| **`upstream`** | Repo asli milik dosen (tempat kamu `pull` pembaruan). |
| **Branch** | Cabang kerja terpisah, supaya `main` tetap bersih. |

Repo dosen pada proyek ini: <https://github.com/mirzayogy/laravel5d>

---

## 1. Persiapan (sekali saja)

### Windows
1. Buat akun di <https://github.com>.
2. Pasang **Git for Windows**: <https://git-scm.com/download/win>. Pilih opsi bawaan saat instalasi.
3. Pasang PHP dan Composer lewat PowerShell (Run as Administrator):
   ```powershell
   Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://php.new/install/windows/8.5'))
   ```
4. Pasang **Node.js LTS**: <https://nodejs.org>.
5. Tutup lalu buka ulang PowerShell, dan cek:
   ```powershell
   git --version
   php -v
   composer -V
   node -v
   ```

### macOS
1. Buat akun di <https://github.com>.
2. Buka **Terminal**. Pasang Homebrew jika belum ada: <https://brew.sh>.
3. Pasang semuanya:
   ```bash
   brew install git node
   /bin/bash -c "$(curl -fsSL https://php.new/install/mac/8.5)"
   ```
4. Tutup lalu buka ulang Terminal, dan cek:
   ```bash
   git --version
   php -v
   composer -V
   node -v
   ```

### Atur identitas Git (Windows & macOS)
```bash
git config --global user.name "Nama Kamu"
git config --global user.email "email-github-kamu@example.com"
```

### Login GitHub dari terminal (disarankan: GitHub CLI)
- **Windows:** `winget install --id GitHub.cli`
- **macOS:** `brew install gh`

Lalu jalankan `gh auth login` dan ikuti petunjuknya (pilih GitHub.com, HTTPS, dan login lewat browser).

---

## 2. Fork repo dosen

### Lewat website (paling mudah)
1. Buka <https://github.com/mirzayogy/laravel5d> dan login.
2. Klik tombol **Fork** di kanan atas.
3. Pilih akun kamu sebagai **Owner**. Nama repo boleh dibiarkan atau diganti.
4. Klik **Create fork**.

Sekarang ada salinan di `https://github.com/USERNAME-KAMU/laravel5d`.

### Lewat terminal (opsional)
```bash
gh repo fork mirzayogy/laravel5d --clone
```
Perintah ini sekaligus mem-fork, meng-clone, dan menambahkan remote `upstream` secara otomatis. Jika memakai cara ini, lompat ke bagian 4.

---

## 3. Clone fork ke laptop

Ganti `USERNAME-KAMU` dengan username GitHub-mu.

```bash
git clone https://github.com/USERNAME-KAMU/laravel5d.git
cd laravel5d
```

- **Windows:** jalankan di PowerShell atau Git Bash. Pilih folder kerja dulu, misalnya `cd C:\Users\NamaKamu\Documents`.
- **macOS:** jalankan di Terminal, misalnya setelah `cd ~/Documents`.

## 4. Hubungkan ke repo dosen (`upstream`)

```bash
git remote add upstream https://github.com/mirzayogy/laravel5d.git
git remote -v
```

Hasil yang benar:
```
origin    https://github.com/USERNAME-KAMU/laravel5d.git (fetch)
origin    https://github.com/USERNAME-KAMU/laravel5d.git (push)
upstream  https://github.com/mirzayogy/laravel5d.git (fetch)
upstream  https://github.com/mirzayogy/laravel5d.git (push)
```

---

## 5. Jalankan proyek Laravel

```bash
composer install
npm install
cp .env.example .env          # Windows PowerShell: copy .env.example .env
php artisan key:generate
php artisan migrate --seed
npm run dev                   # terminal 1
php artisan serve             # terminal 2
```

Buka <http://localhost:8000>.

---

## 6. Alur kerja harian: pakai branch

Jangan bekerja langsung di `main`. Buat branch baru untuk setiap pekerjaan:

```bash
git switch -c feature/nama-fitur
```

Setelah mengubah kode:

```bash
git status                                 # lihat file yang berubah
git add .                                  # siapkan semua perubahan
git commit -m "feat: deskripsi singkat"    # simpan perubahan
git push -u origin feature/nama-fitur      # kirim ke fork-mu
```

Untuk `push` berikutnya di branch yang sama, cukup `git push`.

### Membuat Pull Request (jika diminta dosen)
1. Buka fork-mu di GitHub. Akan muncul banner **Compare & pull request**.
2. Klik banner itu. Pastikan **base repository** adalah repo dosen dan **head repository** adalah fork-mu.
3. Isi judul dan deskripsi, lalu klik **Create pull request**.

Atau lewat terminal: `gh pr create`.

---

## 7. Mengambil pembaruan dari repo dosen

Jika dosen menambahkan materi atau perubahan baru:

```bash
git switch main
git fetch upstream
git merge upstream/main
git push origin main
```

Jika ingin membawa pembaruan itu ke branch kerjamu:
```bash
git switch feature/nama-fitur
git merge main
```

Jika muncul **conflict**, Git menandai file yang bentrok dengan `<<<<<<<`, `=======`, dan `>>>>>>>`. Edit file itu, hapus tanda-tandanya, pilih kode yang benar, lalu:
```bash
git add .
git commit
```

---

## 8. Masalah umum

| Masalah | Solusi |
|---|---|
| `git: command not found` / `'git' is not recognized` | Git belum terpasang atau terminal belum dibuka ulang. |
| `Authentication failed` saat `push` | Jalankan `gh auth login`. GitHub tidak menerima password akun, gunakan login browser atau Personal Access Token. |
| `remote upstream already exists` | Sudah pernah ditambahkan. Cek dengan `git remote -v`. |
| `Permission denied` saat `push` ke repo dosen | Kamu memang tidak boleh `push` ke repo dosen. Lakukan `push` ke `origin` (fork-mu), lalu buat Pull Request. |
| `php artisan migrate` gagal karena driver | Pastikan ekstensi `pdo_sqlite` aktif, atau atur `DB_CONNECTION` di `.env`. |
| `composer install` sangat lambat atau gagal | Cek koneksi internet, lalu jalankan ulang. |
| File `.env` ikut ter-commit | Jangan. File ini sudah ada di `.gitignore`, jadi jangan pakai `git add -f`. |

## 9. Ringkasan perintah

```bash
gh repo fork mirzayogy/laravel5d --clone   # fork + clone
git remote add upstream <url-dosen>         # sekali saja
git switch -c feature/xxx                   # branch baru
git add . && git commit -m "pesan"          # simpan perubahan
git push -u origin feature/xxx              # kirim ke fork
git fetch upstream && git merge upstream/main   # ambil pembaruan dosen
```
