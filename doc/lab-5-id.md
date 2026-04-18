# Dokumentasi Praktikum Administrasi Sistem

> **Pertemuan 5: Keamanan Sistem (Firewall, SSH Hardening, Key Authentication, Monitoring)**
> *Sistem Operasi: Ubuntu Server 22.04 LTS*

---

# 📋 Identitas Praktikan

| Identitas | Keterangan |
|---|---|
| 📝 Nama | `Afrizal Ilza Munadhif` |
| 🆔 NIM | `231240001420` |
| 👤 Username VM | `gix` |
| 🔐 Password | `******` |
| 🌐 IP VM | `192.168.56.10` |
| 🖥️ Hostname | `ubuntu-server` |
| 📅 Tanggal Praktik | `19/04/2026` |

# 📑 Daftar Isi

- [Bab I: Firewall Dasar (UFW)](#bab-i-firewall-dasar-ufw)
  - [1.1 Cek Status Firewall](#11-cek-status-firewall)
  - [1.2 Lihat Aturan Default](#12-lihat-aturan-default)
  - [1.3 Izinkan SSH](#13-izinkan-ssh)
  - [1.4 Aktifkan Firewall](#14-aktifkan-firewall)
  - [1.5 Izinkan Port Lain](#15-izinkan-port-lain)
  - [1.6 Lihat Aturan dengan Nomor](#16-lihat-aturan-dengan-nomor)
  - [1.7 Menghapus Aturan](#17-menghapus-aturan)
  - [1.8 Matikan Firewall](#18-matikan-firewall)
- [Bab II: SSH Hardening](#bab-ii-ssh-hardening)
  - [2.1 Backup Konfigurasi SSH](#21-backup-konfigurasi-ssh)
  - [2.2 Ubah Port SSH](#22-ubah-port-ssh)
  - [2.3 Izinkan Port Baru di Firewall](#23-izinkan-port-baru-di-firewall)
  - [2.4 Nonaktifkan Login Root via SSH](#24-nonaktifkan-login-root-via-ssh)
  - [2.5 Batasi User yang Bisa Login SSH](#25-batasi-user-yang-bisa-login-ssh)
  - [2.6 Restart SSH dan Verifikasi](#26-restart-ssh-dan-verifikasi)
- [Bab III: SSH Key Authentication (Login Tanpa Password)](#bab-iii-ssh-key-authentication-login-tanpa-password)
  - [3.1 Membuat Pasangan Kunci di Laptop](#31-membuat-pasangan-kunci-di-laptop)
  - [3.2 Menyalin Public Key ke Server](#32-menyalin-public-key-ke-server)
  - [3.3 Mengatur Permission Private Key (Windows)](#33-mengatur-permission-private-key-windows)
  - [3.4 Tes Login Tanpa Password](#34-tes-login-tanpa-password)
  - [3.5 Nonaktifkan Password Authentication](#35-nonaktifkan-password-authentication)
- [Bab IV: Monitoring Keamanan Dasar](#bab-iv-monitoring-keamanan-dasar)
  - [4.1 Melihat Log Authentication](#41-melihat-log-authentication)
  - [4.2 Mencari Percobaan Login Gagal](#42-mencari-percobaan-login-gagal)
  - [4.3 Melihat Riwayat Login User](#43-melihat-riwayat-login-user)
  - [4.4 Monitoring Aktif dengan journalctl](#44-monitoring-aktif-dengan-journalctl)
- [Bab V: Ringkasan Command Penting](#bab-v-ringkasan-command-penting)
- [Bab VI: Checklist Keberhasilan](#bab-vi-checklist-keberhasilan)
- [Bab VII: Penutup](#bab-vii-penutup)
- [Bab VIII: Referensi](#bab-viii-referensi)

---

# Bab I: Firewall Dasar (UFW)

> 📖 *Pada bab ini akan dijelaskan cara mengaktifkan dan mengelola firewall menggunakan UFW (Uncomplicated Firewall) di Ubuntu Server.*

## 1.1 Cek Status Firewall

> 🔍 **Langkah 1**: Memeriksa status firewall saat ini

```bash
sudo ufw status
```

### ✅ **Output awal:**
```
Status: inactive
```

> 📝 **Arti**: Firewall belum aktif (default setelah instalasi).

---

## 1.2 Lihat Aturan Default

> 🔍 **Langkah 2**: Melihat aturan default firewall

```bash
sudo ufw show raw
```

### 📋 **Aturan default:**
- **deny incoming** — tolak semua koneksi masuk
- **allow outgoing** — izinkan semua koneksi keluar

---

## 1.3 Izinkan SSH

> 🔑 **Langkah 3**: Membuka port SSH agar tidak terkunci setelah firewall aktif

```bash
sudo ufw allow ssh
# atau
sudo ufw allow 22/tcp
```

### 🔍 **Verifikasi:**
```bash
sudo ufw show added
```

> ⚠️ **Penting**: Izinkan SSH **SEBELUM** mengaktifkan firewall, agar tidak terkunci dari remote access!

---

## 1.4 Aktifkan Firewall

> 🛡️ **Langkah 4**: Mengaktifkan firewall

```bash
sudo ufw enable
```

Akan muncul peringatan, ketik `y` lalu Enter.

### 🔍 **Cek status:**
```bash
sudo ufw status verbose
```

### ✅ **Output:**
```
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
```

---

## 1.5 Izinkan Port Lain

> 🌐 **Langkah 5**: Membuka port tambahan untuk layanan lain

```bash
# Izinkan HTTP (port 80)
sudo ufw allow 80/tcp

# Izinkan HTTPS (port 443)
sudo ufw allow 443/tcp
```

---

## 1.6 Lihat Aturan dengan Nomor

> 📊 **Langkah 6**: Melihat daftar aturan dengan nomor urut

```bash
sudo ufw status numbered
```

### ✅ **Output:**
```
Status: active
     To                         Action      From
     --                         ------      ----
[ 1] 22/tcp                     ALLOW IN    Anywhere
[ 2] 80/tcp                     ALLOW IN    Anywhere
[ 3] 443/tcp                    ALLOW IN    Anywhere
```

---

## 1.7 Menghapus Aturan

> ❌ **Langkah 7**: Menghapus aturan berdasarkan nomor

```bash
# Hapus aturan nomor 3
sudo ufw delete 3
```

> 📝 **Catatan**: Nomor aturan bisa berubah setelah penghapusan. Selalu cek dengan `sudo ufw status numbered` sebelum menghapus.

---

## 1.8 Matikan Firewall

> ⏸️ **Langkah 8**: Menonaktifkan firewall (jika diperlukan)

```bash
sudo ufw disable
```

> ⚠️ **Peringatan**: Mematikan firewall akan membuka semua port. Hanya lakukan untuk troubleshooting!

---

# Bab II: SSH Hardening

> 📖 *Pada bab ini akan dijelaskan cara mengamankan konfigurasi SSH untuk mencegah akses tidak sah.*

## 2.1 Backup Konfigurasi SSH

> 💾 **Langkah 1**: Membuat backup file konfigurasi SSH sebelum perubahan

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

> 📝 **Catatan**: Selalu backup sebelum mengubah konfigurasi penting!

---

## 2.2 Ubah Port SSH

> 🔧 **Langkah 2**: Mengubah port default SSH dari 22 ke 2222

```bash
sudo nano /etc/ssh/sshd_config
```

Cari `#Port 22`, ubah menjadi:

```
Port 2222
```

Simpan: `Ctrl+X`, `Y`, `Enter`.

> 📝 **Alasan**: Mengubah port default mengurangi serangan automated scanner yang menargetkan port 22.

---

## 2.3 Izinkan Port Baru di Firewall

> 🛡️ **Langkah 3**: Membuka port baru di firewall dan menutup port lama

```bash
# Izinkan port baru
sudo ufw allow 2222/tcp

# Hapus aturan port lama
sudo ufw delete allow 22/tcp

# Reload firewall
sudo ufw reload
```

> ⚠️ **Penting**: Buka port baru **SEBELUM** menutup port lama, agar tidak terkunci!

---

## 2.4 Nonaktifkan Login Root via SSH

> 🔒 **Langkah 4**: Melarang login sebagai root melalui SSH

Di file `/etc/ssh/sshd_config`, tambahkan atau ubah:

```
PermitRootLogin no
```

> 📝 **Alasan**: Login root via SSH sangat berbahaya. Gunakan `sudo` dari user biasa sebagai gantinya.

---

## 2.5 Batasi User yang Bisa Login SSH

> 👤 **Langkah 5**: Membatasi user yang diizinkan login via SSH

Tambahkan baris di `/etc/ssh/sshd_config`:

```
AllowUsers gix andi
```

> 📝 **Arti**: Hanya user `gix` dan `andi` yang diizinkan login via SSH. User lain akan ditolak.

---

## 2.6 Restart SSH dan Verifikasi

> 🔄 **Langkah 6**: Merestart service SSH dan memverifikasi konfigurasi

```bash
# Restart SSH
sudo systemctl restart ssh

# Cek status SSH
sudo systemctl status ssh

# Verifikasi port yang listening
sudo ss -tulpn | grep 2222
```

### ✅ **Output yang diharapkan:**
```
tcp   LISTEN 0    128    0.0.0.0:2222   0.0.0.0:*
tcp   LISTEN 0    128    [::]:2222      [::]:*
```

> 📝 **Arti**: SSH sekarang berjalan di port 2222 dan siap menerima koneksi.

---

# Bab III: SSH Key Authentication (Login Tanpa Password)

> 📖 *Pada bab ini akan dijelaskan cara mengatur autentikasi SSH menggunakan kunci kriptografi (key pair) agar bisa login tanpa password.*

## 3.1 Membuat Pasangan Kunci di Laptop

> 🔑 **Langkah 1**: Membuat pasangan kunci (public & private) di laptop

> ⚠️ **JANGAN di VM!** Buka PowerShell/CMD di laptop:

```powershell
ssh-keygen -t ed25519 -C "gix@ubuntu-server"
```

- Tekan **Enter** untuk lokasi default (`~/.ssh/id_ed25519`)
- Masukkan **passphrase kosong** (Enter dua kali)

### ✅ **Output:**
```
Your identification has been saved in ~/.ssh/id_ed25519
Your public key has been saved in ~/.ssh/id_ed25519.pub
```

### 📋 **Penjelasan:**

| File | Keterangan |
|---|---|
| `id_ed25519` | 🔒 Private key — **JANGAN** dibagikan! |
| `id_ed25519.pub` | 🔓 Public key — disalin ke server |

---

## 3.2 Menyalin Public Key ke Server

> 📤 **Langkah 2**: Menyalin public key ke server agar server mengenali laptop

### Cara manual (karena `ssh-copy-id` tidak tersedia di Windows):

```powershell
# Tampilkan public key
type ~/.ssh/id_ed25519.pub
```

Blok dan salin seluruh baris (mulai `ssh-ed25519` sampai akhir).

Di sesi SSH port 2222 (VM):

```bash
mkdir -p ~/.ssh
echo "ssh-ed25519 AAAAC3... gix@ubuntu-server" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### Alternatif dengan satu baris di PowerShell:

```powershell
type ~/.ssh/id_ed25519.pub | ssh -p 2222 gix@192.168.56.10 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

---

## 3.3 Mengatur Permission Private Key (Windows)

> 🔐 **Langkah 3**: Mengatasi error bad permissions pada private key

Jika muncul error bad permissions, buat key baru di folder terpisah (lebih aman):

```powershell
# Buat folder khusus
mkdir C:\Users\TUF GAMING\.ssh-praktik
cd C:\Users\TUF GAMING\.ssh-praktik

# Buat key baru
ssh-keygen -t ed25519 -f ./id_ed25519_praktik -C "praktik@ubuntu-server"

# Salin public key ke server
type .\id_ed25519_praktik.pub | ssh -p 2222 gix@192.168.56.10 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

> 📝 **Catatan**: Folder terpisah menghindari konflik permission dengan key lain yang sudah ada.

---

## 3.4 Tes Login Tanpa Password

> 🧪 **Langkah 4**: Menguji login SSH menggunakan key

Dari terminal laptop (bukan dari sesi SSH yang aktif):

```powershell
ssh -p 2222 -i C:\Users\TUF GAMING\.ssh-praktik\id_ed25519_praktik gix@192.168.56.10
```

> ✅ Jika langsung masuk tanpa password → **berhasil!**

---

## 3.5 Nonaktifkan Password Authentication

> 🔒 **Langkah 5**: Menonaktifkan login dengan password agar hanya key yang diterima

Setelah SSH key berhasil, di sesi SSH port 2222 (VM):

```bash
sudo nano /etc/ssh/sshd_config
```

Cari `PasswordAuthentication yes`, ubah menjadi:

```
PasswordAuthentication no
```

Restart SSH:

```bash
sudo systemctl restart ssh
```

### 🧪 **Tes:**
- Login dengan SSH key → ✅ **Berhasil**
- Login dengan password → ❌ **Ditolak**

> ⚠️ **Peringatan**: Pastikan SSH key sudah berfungsi sebelum menonaktifkan password auth, agar tidak terkunci!

---

# Bab IV: Monitoring Keamanan Dasar

> 📖 *Pada bab ini akan dijelaskan cara memantau aktivitas keamanan sistem melalui log dan monitoring tools.*

## 4.1 Melihat Log Authentication

> 📋 **Langkah 1**: Melihat log percobaan autentikasi

```bash
sudo tail -20 /var/log/auth.log
```

> 📝 **Arti**: Akan terlihat percobaan login (berhasil/gagal) beserta timestamp dan informasi user.

---

## 4.2 Mencari Percobaan Login Gagal

> 🔍 **Langkah 2**: Mendeteksi percobaan login yang gagal (indikasi serangan)

```bash
# Cari login gagal
sudo grep "Failed password" /var/log/auth.log | tail -10
```

Melihat penolakan karena `AllowUsers`:

```bash
sudo grep "not allowed" /var/log/auth.log | tail -5
```

> 📝 **Catatan**: Banyak percobaan login gagal dari IP yang sama bisa menjadi indikasi brute-force attack.

---

## 4.3 Melihat Riwayat Login User

> 📊 **Langkah 3**: Melihat siapa yang pernah login ke sistem

```bash
last -10
```

> 📝 **Arti**: Menampilkan 10 login terakhir dengan informasi user, terminal, waktu, dan IP asal.

---

## 4.4 Monitoring Aktif dengan journalctl

> 📡 **Langkah 4**: Memantau log SSH secara real-time

```bash
sudo journalctl -u ssh -f
```

Tekan `Ctrl+C` untuk berhenti.

> 📝 **Arti**: Flag `-f` (follow) menampilkan log secara real-time, berguna untuk memantau aktivitas SSH saat sedang berlangsung.

---

# Bab V: Ringkasan Command Penting

> 📚 *Ringkasan command-command penting yang dipelajari di pertemuan ini.*

## 🛡️ UFW (Firewall)

| Perintah | Fungsi |
|---|---|
| `sudo ufw status` | Cek status firewall |
| `sudo ufw enable` | Aktifkan firewall |
| `sudo ufw disable` | Matikan firewall |
| `sudo ufw allow port/tcp` | Izinkan port |
| `sudo ufw delete nomor` | Hapus aturan |
| `sudo ufw status numbered` | Lihat aturan dengan nomor |
| `sudo ufw show added` | Lihat aturan yang ditambahkan |
| `sudo ufw reload` | Reload aturan firewall |

## 🔧 SSH Hardening

| Perintah | Fungsi |
|---|---|
| `sudo nano /etc/ssh/sshd_config` | Edit konfigurasi SSH |
| `sudo systemctl restart ssh` | Restart SSH |
| `sudo systemctl status ssh` | Cek status SSH |
| `sudo ss -tulpn \| grep ssh` | Cek port SSH yang listening |

## 🔑 SSH Key

| Perintah (di laptop) | Fungsi |
|---|---|
| `ssh-keygen -t ed25519` | Buat pasangan kunci |
| `type ~/.ssh/id_ed25519.pub` | Tampilkan public key |
| `ssh -p port -i key user@ip` | Login dengan key |

## 📊 Monitoring

| Perintah | Fungsi |
|---|---|
| `sudo tail -20 /var/log/auth.log` | Lihat log authentication |
| `sudo grep "Failed password" /var/log/auth.log` | Cari login gagal |
| `sudo grep "not allowed" /var/log/auth.log` | Cari user yang ditolak |
| `last -10` | Lihat riwayat login |
| `sudo journalctl -u ssh -f` | Monitor log SSH real-time |

---

# Bab VI: Checklist Keberhasilan

> ✅ **Checklist verifikasi** untuk memastikan semua konfigurasi berjalan dengan baik

## 🛡️ Firewall (UFW)

| No | Item | Verifikasi | Status |
|---|---|---|---|
| 1 | 🛡️ UFW aktif | `sudo ufw status verbose` | ✅ |
| 2 | 🔓 Port 2222 diizinkan | `sudo ufw status numbered` | ✅ |
| 3 | 🔒 Port 22 ditolak (setelah migrasi) | `sudo ufw status numbered` | ✅ |

## 🔧 SSH Hardening

| No | Item | Verifikasi | Status |
|---|---|---|---|
| 1 | 🔧 SSH berjalan di port 2222 | `sudo ss -tulpn \| grep 2222` | ✅ |
| 2 | 🔒 Login root via SSH dinonaktifkan | `grep PermitRootLogin /etc/ssh/sshd_config` | ✅ |
| 3 | 👤 Hanya user gix dan andi yang diizinkan | `grep AllowUsers /etc/ssh/sshd_config` | ✅ |

## 🔑 SSH Key Authentication

| No | Item | Verifikasi | Status |
|---|---|---|---|
| 1 | 🔑 Pasangan kunci berhasil dibuat di laptop | `ls ~/.ssh/id_ed25519*` | ✅ |
| 2 | 📤 Public key berhasil disalin ke server | `cat ~/.ssh/authorized_keys` | ✅ |
| 3 | ✅ Login tanpa password berhasil | `ssh -p 2222 -i key gix@ip` | ✅ |
| 4 | 🔒 Password authentication dinonaktifkan | `grep PasswordAuthentication /etc/ssh/sshd_config` | ✅ |

## 📊 Monitoring

| No | Item | Verifikasi | Status |
|---|---|---|---|
| 1 | 📋 Bisa melihat log authentication | `sudo tail /var/log/auth.log` | ✅ |
| 2 | 🔍 Bisa mencari percobaan login gagal | `sudo grep "Failed password" /var/log/auth.log` | ✅ |
| 3 | 📊 Bisa melihat riwayat login | `last -10` | ✅ |
| 4 | 📡 Bisa monitor log SSH real-time | `sudo journalctl -u ssh -f` | ✅ |

---

# Bab VII: Penutup

> 🎉 Demikian dokumentasi praktikum Administrasi Sistem Pertemuan 5: Keamanan Sistem.

Dengan selesainya dokumentasi ini, praktikan telah menguasai:

- 🛡️ **Firewall UFW** — mengaktifkan, membuka/menutup port, melihat aturan
- 🔧 **SSH Hardening** — mengganti port, menonaktifkan root login, membatasi user
- 🔑 **SSH Key Authentication** — login tanpa password dengan kunci kriptografi
- 📊 **Monitoring Keamanan** — membaca log authentication, mendeteksi serangan

---

# Bab VIII: Referensi

> 📚 **Referensi dan dokumentasi** yang digunakan selama praktikum

## 🛡️ UFW Documentation
- **Website**: [https://help.ubuntu.com/community/UFW](https://help.ubuntu.com/community/UFW)
- **Topik**: UFW basics, allowing/denying ports, status commands

## 🔧 SSH Hardening
- **Website**: [https://www.ssh.com/academy/ssh/sshd_config](https://www.ssh.com/academy/ssh/sshd_config)
- **Topik**: sshd_config options, PermitRootLogin, AllowUsers

## 🔑 SSH Key Authentication
- **Website**: [https://www.ssh.com/academy/ssh/keygen](https://www.ssh.com/academy/ssh/keygen)
- **Topik**: ssh-keygen, ed25519, authorized_keys

## 📊 Monitoring & Logging
- **Website**: [https://manpages.ubuntu.com/manpages/](https://manpages.ubuntu.com/manpages/)
- **Topik**: journalctl, auth.log, last, grep

### 🛡️ **Best Practices**
- Selalu izinkan SSH sebelum mengaktifkan firewall
- Selalu backup konfigurasi sebelum mengubah
- Gunakan port non-standar untuk SSH
- Nonaktifkan login root via SSH
- Batasi user yang bisa login SSH
- Gunakan SSH key sebagai pengganti password
- Monitor log secara berkala untuk deteksi serangan

---

> 🎯 **Kesimpulan**: Dokumentasi ini mencakup seluruh praktikum Keamanan Sistem untuk praktikum Administrasi Sistem Pertemuan 5.

> 📅 **Terakhir diperbarui**: `[19/04/2026]`
> 👨‍💻 **Oleh**: `[Afrizal Ilza Munadhif]` - `[231240001420]`
> 🔗 **Repository**: [https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab](https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab)
