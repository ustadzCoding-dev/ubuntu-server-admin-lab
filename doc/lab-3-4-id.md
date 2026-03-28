# Dokumentasi Praktikum Administrasi Sistem

> **Pertemuan 3–4: Manajemen User, Grup, dan Hak Akses File**
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
| 📅 Tanggal Praktik | `29/03/2026` |

# 📑 Daftar Isi

## 📑 Table of Contents

- [Bab I: Manajemen User](#bab-i-manajemen-user)
  - [1.1 Memahami File Konfigurasi User](#11-memahami-file-konfigurasi-user)
  - [1.2 Membuat User dengan adduser](#12-membuat-user-dengan-adduser)
  - [1.3 Membuat User dengan useradd](#13-membuat-user-dengan-useradd)
  - [1.4 Membuat User dengan Primary Group Tertentu](#14-membuat-user-dengan-primary-group-tertentu)
  - [1.5 Melihat Informasi User](#15-melihat-informasi-user)
  - [1.6 Login Sebagai User Lain](#16-login-sebagai-user-lain)
  - [1.7 Mengubah User (usermod)](#17-mengubah-user-usermod)
  - [1.8 Menghapus User (userdel)](#18-menghapus-user-userdel)
- [Bab II: Manajemen Grup](#bab-ii-manajemen-grup)
  - [2.1 Membuat Grup](#21-membuat-grup)
  - [2.2 Menambah User ke Grup Supplementary](#22-menambah-user-ke-grup-supplementary)
  - [2.3 Melihat Informasi Grup](#23-melihat-informasi-grup)
  - [2.4 Primary Group vs Supplementary Group](#24-primary-group-vs-supplementary-group)
  - [2.5 Mengubah dan Menghapus Grup](#25-mengubah-dan-menghapus-grup)
- [Bab III: Hak Akses File (Permission)](#bab-iii-hak-akses-file-permission)
  - [3.1 Memahami Permission rwx](#31-memahami-permission-rwx)
  - [3.2 Mengubah Permission dengan Numeric (chmod)](#32-mengubah-permission-dengan-numeric-chmod)
  - [3.3 Mengubah Permission dengan Symbolic](#33-mengubah-permission-dengan-symbolic)
  - [3.4 Mengubah Ownership (chown, chgrp)](#34-mengubah-ownership-chown-chgrp)
- [Bab IV: Struktur Folder dan Permission](#bab-iv-struktur-folder-dan-permission)
  - [4.1 Membuat Struktur Folder](#41-membuat-struktur-folder)
  - [4.2 Folder Publik (755)](#42-folder-publik-755)
  - [4.3 Folder Tim dengan Grup (770)](#43-folder-tim-dengan-grup-770)
  - [4.4 Folder Private (700)](#44-folder-private-700)
  - [4.5 Sticky Bit (1777)](#45-sticky-bit-1777)
- [Bab V: Praktik Hapus User dan Grup](#bab-v-praktik-hapus-user-dan-grup)
  - [5.1 Menghapus User (userdel)](#51-menghapus-user-userdel)
  - [5.2 Menghapus Grup (groupdel)](#52-menghapus-grup-groupdel)
  - [5.3 Memeriksa User dan Grup yang Dihapus](#53-memeriksa-user-dan-grup-yang-dihapus)
- [Bab VI: Ringkasan Command Penting](#bab-vi-ringkasan-command-penting)
- [Bab VII: Checklist Keberhasilan](#bab-vii-checklist-keberhasilan)
- [Bab VIII: Catatan Praktikan](#bab-viii-catatan-praktikan)
- [Bab IX: Referensi](#bab-ix-referensi)

---

# Bab I: Manajemen User

> 📖 *Pada bab ini akan dijelaskan cara membuat, mengubah, dan mengelola user di sistem Ubuntu Server.*

## 1.1 Memahami File Konfigurasi User

> 🔍 **Langkah 1**: Melihat semua user dengan home directory

```bash
# Lihat semua user dengan home directory
cat /etc/passwd | grep /home
```

### ✅ **Output awal:**
```
gix:x:1000:1000:gix:/home/gix:/usr/bin/zsh
```

### 📋 **Format /etc/passwd:**
```
username:password:UID:GID:comment:home:shell
```

| Field | Contoh | Keterangan |
|---|---|---|
| username | gix | Nama login |
| password | x | Password tersimpan di /etc/shadow |
| UID | 1000 | User ID (0=root, 1000=user pertama) |
| GID | 1000 | Group ID (primary group) |
| comment | gix | Nama lengkap user |
| home | /home/gix | Home directory |
| shell | /usr/bin/zsh | Default shell |

> 🔍 **Langkah 2**: Melihat password terenkripsi

```bash
# Lihat password terenkripsi (hanya root)
sudo cat /etc/shadow | grep gix

# Format: username:encrypted_password:last_change:min:max:warn:inactive:expire
```

---

## 1.2 Membuat User dengan adduser

> 📝 **Membuat user baru** dengan cara interaktif

```bash
# Buat user andi dengan adduser (interaktif)
sudo adduser andi
```

### ✅ **Isi data:**
```
Password: andi123
Full Name []: Andi Setiawan
Room Number []: (kosong)
Work Phone []: (kosong)
Home Phone []: (kosong)
Other []: (kosong)
Is the information correct? [Y/n]: Y
```

### 🔍 **Verifikasi:**
```bash
cat /etc/passwd | grep andi
# Output: andi:x:1001:1001:Andi Setiawan,,,:/home/andi:/bin/bash
```

---

## 1.3 Membuat User dengan useradd

> ⚙️ **Membuat user baru** dengan cara non-interaktif

```bash
# Buat user siti dengan useradd (non-interaktif)
sudo useradd -m -s /bin/bash siti

# Set password untuk siti
sudo passwd siti
# New password: siti123
# Retype new password: siti123
```

### 📋 **Penjelasan opsi useradd:**

| Opsi | Keterangan |
|---|---|
| `-m` | Membuat home directory |
| `-s /bin/bash` | Menentukan shell default |
| `-g group` | Menentukan primary group |
| `-G group1,group2` | Menambah ke supplementary group |
| `-c "komentar"` | Menambahkan komentar/nama lengkap |

---

## 1.4 Membuat User dengan Primary Group Tertentu

> 🎯 **Membuat user** dengan primary group yang sudah ditentukan

```bash
# Buat grup webdev dulu
sudo groupadd webdev

# Buat user budi dengan primary group webdev
sudo useradd -m -s /bin/bash -g webdev budi

# Set password
sudo passwd budi
# Password: budi123
```

### 🔍 **Verifikasi:**
```bash
id budi
# Output: uid=1003(budi) gid=1003(webdev) groups=1003(webdev)
```

---

## 1.5 Melihat Informasi User

> 📊 **Melihat informasi lengkap** tentang user

```bash
# Lihat informasi lengkap user
id andi
# Output: uid=1001(andi) gid=1001(andi) groups=1001(andi)

id siti
# Output: uid=1002(siti) gid=1002(siti) groups=1002(siti)

id budi
# Output: uid=1003(budi) gid=1003(webdev) groups=1003(webdev)

# Lihat user yang sedang login
who
# Output: gix     pts/0        2026-03-28 10:00 (192.168.56.1)

# Lihat history login
last -5

# Lihat user berdasarkan shell
cat /etc/passwd | grep /bin/bash
```

---

## 1.6 Login Sebagai User Lain

> 🔐 **Beralih ke user lain** untuk testing atau administrasi

```bash
# Login sebagai andi
sudo su - andi
pwd               # /home/andi
whoami            # andi
exit              # Kembali ke gix

# Login sebagai siti
sudo su - siti
pwd               # /home/siti
exit

# Login sebagai budi
sudo su - budi
pwd               # /home/budi
id                # uid=1003(budi) gid=1003(webdev)
exit
```

---

## 1.7 Mengubah User (usermod)

> ✏️ **Mengubah properti user** yang sudah ada

```bash
# Ganti shell user
sudo usermod -s /bin/zsh andi
# Cek: grep andi /etc/passwd → /home/andi:/bin/zsh

# Ganti nama user (login name)
sudo usermod -l andi_baru andi
# Cek: grep andi /etc/passwd → andi_baru:x:1001:...

# Kembalikan nama (jika perlu)
sudo usermod -l andi andi_baru

# Ganti primary group
sudo usermod -g webdev siti
id siti
# Output: uid=1002(siti) gid=1003(webdev) groups=1003(webdev)

# Kembalikan primary group
sudo usermod -g siti siti

# Ganti home directory
sudo usermod -d /home/andi_new andi
```

---

## 1.8 Menghapus User (userdel)

> ❌ **Menghapus user** dari sistem

### 📋 **Langkah 1: Buat user temporary untuk latihan hapus**
```bash
# Buat user temporary untuk latihan hapus
sudo useradd -m -s /bin/bash tempuser
sudo passwd tempuser
# Password: temp123

# Cek user sudah ada
cat /etc/passwd | grep tempuser
# Output: tempuser:x:1004:1004::/home/tempuser:/bin/bash
```

### 📋 **Langkah 2: Hapus user TANPA menghapus home directory**
```bash
# Hapus user TANPA menghapus home directory
sudo userdel tempuser

# Cek: user sudah tidak ada di /etc/passwd
cat /etc/passwd | grep tempuser
# Output: (kosong)

# Home directory masih ada
ls -la /home/ | grep tempuser
# Output: drwxr-x--- 3 1004 1004 4096 ... tempuser
```

### 📋 **Langkah 3: Hapus user BESERTA home directory**
```bash
# Buat user baru untuk latihan hapus dengan home
sudo useradd -m -s /bin/bash tempuser2
sudo passwd tempuser2
# Password: temp123

# Hapus user BESERTA home directory
sudo userdel -r tempuser2

# Cek: user dan home sudah hilang
cat /etc/passwd | grep tempuser2
ls -la /home/ | grep tempuser2
# Output: (kosong)
```

### 📊 **Perbedaan userdel:**

| Perintah | Home Directory | File Lain | Kapan Pakai |
|---|---|---|---|
| `sudo userdel nama` | Tetap ada | Tetap | User berhenti, data perlu diarsip |
| `sudo userdel -r nama` | Terhapus | Terhapus | User selesai, tidak perlu data |

```bash
# Hapus file yang tertinggal dari user yang sudah dihapus
# Cari file dengan UID yang tidak ada
find /home -nouser -ls
```

---

# Bab II: Manajemen Grup

> 👥 *Pada bab ini akan dijelaskan cara membuat, mengubah, dan mengelola grup di sistem Ubuntu Server.*

## 2.1 Membuat Grup

> ➕ **Membuat grup baru** untuk organisasi user

```bash
# Buat grup baru
sudo groupadd webdev
sudo groupadd project_alpha
sudo groupadd marketing

# Buat grup dengan GID tertentu
sudo groupadd -g 2000 sales

# Lihat grup yang sudah dibuat
cat /etc/group | grep -E "webdev|project_alpha|marketing|sales"
```

### ✅ **Output:**
```
webdev:x:1003:
project_alpha:x:1004:
marketing:x:1005:
sales:x:2000:
```

---

## 2.2 Menambah User ke Grup Supplementary

> 🔗 **Menambahkan user** ke grup supplementary

```bash
# Tambah andi ke grup webdev
sudo usermod -aG webdev andi

# Tambah siti ke grup webdev dan project_alpha
sudo usermod -aG webdev,project_alpha siti

# Tambah andi ke grup marketing
sudo usermod -aG marketing andi
```

### 📋 **Penjelasan:**
- `-a` = append (tambah, jangan hapus grup lain)
- `-G` = specify supplementary group

### 🔍 **Verifikasi:**
```bash
id andi
# Output: uid=1001(andi) gid=1001(andi) groups=1001(andi),1003(webdev),1005(marketing)

id siti
# Output: uid=1002(siti) gid=1002(siti) groups=1002(siti),1003(webdev),1004(project_alpha)
```

---

## 2.3 Melihat Informasi Grup

> 📊 **Melihat informasi** tentang grup dan anggotanya

```bash
# Lihat semua grup
cat /etc/group

# Lihat grup tertentu dan anggotanya
cat /etc/group | grep webdev
# Output: webdev:x:1003:andi,siti
# Catatan: budi tidak muncul karena dia primary group, bukan supplementary

# Lihat grup yang dimiliki user
groups andi
# Output: andi webdev marketing

groups siti
# Output: siti webdev project_alpha
```

---

## 2.4 Primary Group vs Supplementary Group

> 🔄 **Perbedaan** antara primary group dan supplementary group

| Tipe | Cara Cek | Tampilan di /etc/group | Contoh |
|---|---|---|---|
| Primary Group | `id budi` → gid=1003(webdev) | Members kosong | webdev:x:1003: |
| Supplementary Group | `id andi` → groups=1003(webdev) | Ada nama user | webdev:x:1003:andi,siti |

### 📋 **Contoh:**
```bash
# budi adalah primary group webdev
id budi
# uid=1003(budi) gid=1003(webdev) groups=1003(webdev)

# andi adalah supplementary webdev
id andi
# uid=1001(andi) gid=1001(andi) groups=1001(andi),1003(webdev)
```

---

## 2.5 Mengubah dan Menghapus Grup

> ✏️ **Mengubah dan menghapus** grup yang sudah ada

```bash
# Ubah nama grup
sudo groupmod -n dev webdev
# webdev menjadi dev

# Ubah GID grup
sudo groupmod -g 3000 dev
# GID dev menjadi 3000

# Kembalikan nama (untuk konsistensi praktik)
sudo groupmod -n webdev dev
sudo groupmod -g 1003 webdev

# Buat grup temporary untuk latihan hapus
sudo groupadd tempgroup

# Hapus grup (pastikan tidak ada anggota)
sudo groupdel tempgroup
```

> ⚠️ **Peringatan**: Grup tidak bisa dihapus jika masih ada user dengan primary group tersebut.

```bash
# Contoh: grup webdev tidak bisa dihapus karena budi primary group-nya webdev
sudo groupdel webdev
# Output: groupdel: cannot remove the primary group of user 'budi'
```

---

# Bab III: Hak Akses File (Permission)

> 🔐 *Pada bab ini akan dijelaskan konsep permission file dan cara mengubahnya.*

## 3.1 Memahami Permission rwx

> 🔍 **Melihat permission** file dan direktori

```bash
# Lihat permission file
ls -l
```

### ✅ **Output contoh:**
```
-rwxr-xr-- 1 gix developer 1024 Mar 28 10:00 script.sh
```

### 📋 **Penjelasan permission:**

| Posisi | Arti |
|---|---|
| `-` | File (atau `d` untuk direktori) |
| `rwx` | Owner (gix): read, write, execute |
| `r-x` | Group (developer): read, execute |
| `r--` | Others: read |

### 📊 **Numeric Permission:**

| Angka | Binary | Permission |
|---|---|---|
| 7 | 111 | rwx |
| 6 | 110 | rw- |
| 5 | 101 | r-x |
| 4 | 100 | r-- |
| 3 | 011 | -wx |
| 2 | 010 | -w- |
| 1 | 001 | --x |
| 0 | 000 | --- |

---

## 3.2 Mengubah Permission dengan Numeric (chmod)

> 🔢 **Mengubah permission** menggunakan angka

```bash
# Buat file test
touch ~/test_permissions.txt
ls -l ~/test_permissions.txt
# Output: -rw-rw-r-- 1 gix gix 0 Mar 28 16:03 test_permissions.txt

# Ubah ke 700 (rwx------)
chmod 700 ~/test_permissions.txt
ls -l ~/test_permissions.txt
# Output: -rwx------ 1 gix gix 0 Mar 28 16:03 test_permissions.txt

# Ubah ke 750 (rwxr-x---)
chmod 750 ~/test_permissions.txt
ls -l ~/test_permissions.txt
# Output: -rwxr-x--- 1 gix gix 0 Mar 28 16:03 test_permissions.txt

# Ubah ke 755 (rwxr-xr-x)
chmod 755 ~/test_permissions.txt
ls -l ~/test_permissions.txt
# Output: -rwxr-xr-x 1 gix gix 0 Mar 28 16:03 test_permissions.txt

# Ubah ke 644 (rw-r--r--)
chmod 644 ~/test_permissions.txt
ls -l ~/test_permissions.txt
# Output: -rw-r--r-- 1 gix gix 0 Mar 28 16:03 test_permissions.txt
```

---

## 3.3 Mengubah Permission dengan Symbolic

> 🔤 **Mengubah permission** menggunakan simbol

```bash
# Buat ulang file test
echo "test" > ~/test_sym.txt
ls -l ~/test_sym.txt
# Output: -rw-rw-r-- 1 gix gix 5 Mar 28 16:10 test_sym.txt

# Tambah execute untuk owner (u+x)
chmod u+x ~/test_sym.txt
ls -l ~/test_sym.txt
# Output: -rwxrw-r-- 1 gix gix 5 Mar 28 16:10 test_sym.txt

# Hapus write untuk group (g-w)
chmod g-w ~/test_sym.txt
ls -l ~/test_sym.txt
# Output: -rwxr--r-- 1 gix gix 5 Mar 28 16:10 test_sym.txt

# Set permission untuk group dan others (g=rx,o=rx)
chmod g=rx,o=rx ~/test_sym.txt
ls -l ~/test_sym.txt
# Output: -rwxr-xr-x 1 gix gix 5 Mar 28 16:10 test_sym.txt

# Set owner=rwx, group=rx, other=none (setara 750)
chmod u=rwx,g=rx,o= ~/test_sym.txt
ls -l ~/test_sym.txt
# Output: -rwxr-x--- 1 gix gix 5 Mar 28 16:10 test_sym.txt
```

### 📋 **Symbolic Syntax:**

| Target | Kode | Operator | Permission |
|---|---|---|---|
| user/owner | u | `+` (tambah) | r (read) |
| group | g | `-` (hapus) | w (write) |
| others | o | `=` (set) | x (execute) |
| all | a | | |

---

## 3.4 Mengubah Ownership (chown, chgrp)

> 👤 **Mengubah kepemilikan** file dan direktori

```bash
# Buat file test
touch ~/test_ownership.txt
ls -l ~/test_ownership.txt
# Output: -rw-rw-r-- 1 gix gix 0 Mar 28 16:15 test_ownership.txt

# Ubah owner file
sudo chown andi ~/test_ownership.txt
ls -l ~/test_ownership.txt
# Output: -rw-rw-r-- 1 andi gix 0 Mar 28 16:15 test_ownership.txt

# Ubah owner dan group
sudo chown gix:webdev ~/test_ownership.txt
ls -l ~/test_ownership.txt
# Output: -rw-rw-r-- 1 gix webdev 0 Mar 28 16:15 test_ownership.txt

# Ubah group saja
sudo chgrp project_alpha ~/test_ownership.txt
ls -l ~/test_ownership.txt
# Output: -rw-rw-r-- 1 gix project_alpha 0 Mar 28 16:15 test_ownership.txt

# Recursive (untuk direktori)
sudo chown -R andi:webdev /data/test
```

---

# Bab IV: Struktur Folder dan Permission

> 📁 *Pada bab ini akan dijelaskan cara membuat struktur folder dengan permission yang sesuai.*

## 4.1 Membuat Struktur Folder

> 🏗️ **Membuat struktur folder** untuk organisasi data

```bash
# Buat struktur folder
sudo mkdir -p /data/{public,team,private,backup}

# Lihat struktur
ls -la /data/
```

### ✅ **Output:**
```
total 20
drwxr-xr-x 6 root root 4096 Mar 28 15:47 .
drwxr-xr-x 4 root root 4096 Mar 28 15:47 ..
drwxr-xr-x 2 root root 4096 Mar 28 15:47 backup
drwxr-xr-x 2 root root 4096 Mar 28 15:47 private
drwxr-xr-x 2 root root 4096 Mar 28 15:47 public
drwxr-xr-x 2 root root 4096 Mar 28 15:47 team
```

---

## 4.2 Folder Publik (755)

> 🌐 **Konfigurasi folder publik** yang bisa diakses semua user

```bash
# Set permission untuk folder publik
sudo chmod 755 /data/public
sudo chown root:root /data/public

# Cek
ls -ld /data/public
# Output: drwxr-xr-x 2 root root 4096 Mar 28 15:47 /data/public
```

> 📝 **Arti**: Semua user bisa membaca/melihat isi folder, hanya root yang bisa menulis.

### 🧪 **Tes sebagai user biasa:**
```bash
sudo su - andi
ls /data/public          # ✅ Bisa
touch /data/public/test.txt  # ❌ Permission denied
exit
```

---

## 4.3 Folder Tim dengan Grup (770)

> 👥 **Konfigurasi folder tim** yang hanya bisa diakses anggota grup

```bash
# Buat grup khusus
sudo groupadd project_alpha

# Tambah andi dan siti ke grup
sudo usermod -aG project_alpha andi
sudo usermod -aG project_alpha siti

# Set folder team
sudo chown :project_alpha /data/team
sudo chmod 770 /data/team

# Cek
ls -ld /data/team
# Output: drwxrwx--- 2 root project_alpha 4096 Mar 28 15:47 /data/team
```

> 📝 **Arti**: Hanya owner (root) dan anggota grup project_alpha yang bisa akses.

### 🧪 **Tes:**
```bash
sudo su - andi
cd /data/team && touch andi.txt && ls    # ✅ Bisa
exit

sudo su - budi
cd /data/team    # ❌ Permission denied
exit
```

---

## 4.4 Folder Private (700)

> 🔒 **Konfigurasi folder private** yang hanya bisa diakses owner

```bash
# Buat folder private untuk masing-masing user
sudo mkdir /data/private/andi
sudo mkdir /data/private/siti

# Set ownership
sudo chown andi:andi /data/private/andi
sudo chown siti:siti /data/private/siti

# Set permission
sudo chmod 700 /data/private/andi
sudo chmod 700 /data/private/siti

# Set permission parent folder
sudo chmod 750 /data/private

# Cek
ls -ld /data/private/*
```

### ✅ **Output:**
```
drwx------ 2 andi andi 4096 Mar 28 15:54 /data/private/andi
drwx------ 2 siti siti 4096 Mar 28 15:54 /data/private/siti
```

### 🧪 **Tes:**
```bash
sudo su - andi
cd /data/private/andi && touch andi.txt && ls    # ✅ Bisa
cd /data/private/siti                            # ❌ Permission denied
exit
```

---

## 4.5 Sticky Bit (1777)

> 🛡️ **Konfigurasi sticky bit** untuk folder bersama

> 📝 **Sticky Bit** digunakan pada folder bersama agar user hanya bisa menghapus file miliknya sendiri.

```bash
# Pasang sticky bit
sudo chmod 1777 /data/backup

# Cek
ls -ld /data/backup
# Output: drwxrwxrwt 2 root root 4096 Mar 28 15:47 /data/backup
```

> ⚠️ **Perhatikan** huruf `t` di akhir permission!

### 🧪 **Uji Sticky Bit:**
```bash
# Login sebagai andi
sudo su - andi

# Buat file (gunakan redirect >)
echo "File dari andi" > /data/backup/andi.txt
ls -l /data/backup/
exit

# Login sebagai siti
sudo su - siti

# Buat file sendiri
echo "File dari siti" > /data/backup/siti.txt
ls -l /data/backup/

# Coba hapus file milik andi
rm /data/backup/andi.txt
# Output: rm: cannot remove '/data/backup/andi.txt': Operation not permitted

# Hapus file sendiri (berhasil)
rm /data/backup/siti.txt
exit
```

---

# Bab V: Praktik Hapus User dan Grup

> 🛠️ *Pada bab ini akan dipraktikkan cara menghapus user dan grup dengan benar.*

## 5.1 Menghapus User (userdel)

> ❌ **Menghapus user** dari sistem

### 📋 **Langkah 1: Buat user temporary untuk latihan hapus**
```bash
# Buat user tempuser
sudo useradd -m -s /bin/bash tempuser
sudo passwd tempuser
# Password: temp123

# Buat user tempuser2
sudo useradd -m -s /bin/bash tempuser2
sudo passwd tempuser2
# Password: temp123
```

### 📋 **Langkah 2: Verifikasi user sudah dibuat**
```bash
# Cek di /etc/passwd
cat /etc/passwd | grep tempuser
# Output: tempuser:x:1004:1004::/home/tempuser:/bin/bash

cat /etc/passwd | grep tempuser2
# Output: tempuser2:x:1005:1005::/home/tempuser2:/bin/bash

# Cek home directory
ls -la /home/ | grep tempuser
# Output: drwxr-x--- 3 tempuser tempuser 4096 ... tempuser
ls -la /home/ | grep tempuser2
# Output: drwxr-x--- 3 tempuser2 tempuser2 4096 ... tempuser2
```

### 📋 **Langkah 3: Hapus user TANPA menghapus home directory**
```bash
# Hapus tempuser (tanpa -r)
sudo userdel tempuser

# Cek user sudah tidak ada
cat /etc/passwd | grep tempuser
# Output: (kosong)

# Home directory MASIH ada
ls -la /home/ | grep tempuser
# Output: drwxr-x--- 3 1004 1004 4096 ... tempuser
```

### 📋 **Langkah 4: Hapus user BESERTA home directory**
```bash
# Hapus tempuser2 (dengan -r)
sudo userdel -r tempuser2

# Cek user sudah tidak ada
cat /etc/passwd | grep tempuser2
# Output: (kosong)

# Home directory JUGA hilang
ls -la /home/ | grep tempuser2
# Output: (kosong)
```

### 📋 **Langkah 5: Bersihkan home directory yang tertinggal**
```bash
# Home directory tempuser masih ada dengan owner UID 1004 (tidak ada usernya)
# Hapus manual jika diperlukan
sudo rm -rf /home/tempuser
```

### 📊 **Perbandingan userdel:**

| Perintah | Home Directory | File Lain | Kapan Pakai |
|---|---|---|---|
| `sudo userdel nama` | Tetap ada | Tetap | User berhenti, data perlu diarsip |
| `sudo userdel -r nama` | Terhapus | Terhapus | User selesai, tidak perlu data |

---

## 5.2 Menghapus Grup (groupdel)

> ❌ **Menghapus grup** dari sistem

### 📋 **Langkah 1: Buat grup temporary**
```bash
# Buat grup untuk latihan
sudo groupadd tempgroup
sudo groupadd tempgroup2

# Cek grup
cat /etc/group | grep tempgroup
# Output: tempgroup:x:1006:
cat /etc/group | grep tempgroup2
# Output: tempgroup2:x:1007:
```

### 📋 **Langkah 2: Hapus grup kosong**
```bash
# Hapus tempgroup2
sudo groupdel tempgroup2

# Cek sudah hilang
cat /etc/group | grep tempgroup2
# Output: (kosong)
```

### 📋 **Langkah 3: Coba hapus grup yang masih punya anggota**
```bash
# Buat user dengan primary group tempgroup
sudo useradd -m -g tempgroup tempuser3
sudo passwd tempuser3
# Password: temp123

# Coba hapus grup tempgroup
sudo groupdel tempgroup
```

### ✅ **Output yang diharapkan:**
```
groupdel: cannot remove the primary group of user 'tempuser3'
```

> 📝 **Penjelasan**: Grup tidak bisa dihapus jika masih ada user dengan primary group tersebut.

### 📋 **Langkah 4: Hapus user terlebih dahulu**
```bash
# Hapus user yang masih menggunakan grup
sudo userdel -r tempuser3

# Sekarang grup tempgroup bisa dihapus
sudo groupdel tempgroup

# Verifikasi
cat /etc/group | grep tempgroup
# Output: (kosong)
```

---

## 5.3 Memeriksa User dan Grup yang Dihapus

> 🔍 **Memeriksa file orphaned** dari user/grup yang sudah dihapus

```bash
# Cari file dengan UID yang tidak ada (orphaned files)
find /home -nouser -ls 2>/dev/null

# Cari file dengan GID yang tidak ada
find /home -nogroup -ls 2>/dev/null

# Bersihkan jika ada
sudo find /home -nouser -delete 2>/dev/null
```

---

# Bab VI: Ringkasan Command Penting

> 📚 *Ringkasan command-command penting yang dipelajari di pertemuan ini.*

## 👤 Manajemen User

| Perintah | Fungsi |
|---|---|
| `sudo adduser nama` | Buat user baru (interaktif) |
| `sudo useradd -m -s /bin/bash nama` | Buat user baru (non-interaktif) |
| `sudo passwd nama` | Ganti password user |
| `sudo usermod -aG grup nama` | Tambah user ke grup supplementary |
| `sudo usermod -l nama_baru nama_lama` | Ganti nama user |
| `sudo usermod -s /bin/zsh nama` | Ganti shell user |
| `sudo userdel nama` | Hapus user (home tetap) |
| `sudo userdel -r nama` | Hapus user beserta home directory |
| `id nama` | Lihat informasi user |
| `who` | Lihat user yang sedang login |
| `last` | Lihat history login |
| `cat /etc/passwd` | Lihat semua user |

## 👥 Manajemen Grup

| Perintah | Fungsi |
|---|---|
| `sudo groupadd nama` | Buat grup baru |
| `sudo groupmod -n nama_baru nama_lama` | Ubah nama grup |
| `sudo groupmod -g GID_baru nama` | Ubah GID grup |
| `sudo groupdel nama` | Hapus grup (harus kosong) |
| `cat /etc/group` | Lihat semua grup |
| `groups nama` | Lihat grup yang dimiliki user |

## 🔐 Hak Akses File

| Perintah | Fungsi |
|---|---|
| `chmod 755 file` | Ubah permission dengan numeric |
| `chmod u+x,g=rx file` | Ubah permission dengan symbolic |
| `sudo chown user:group file` | Ubah owner dan group |
| `sudo chgrp group file` | Ubah group saja |
| `ls -l file` | Lihat permission |
| `ls -ld direktori` | Lihat permission direktori |

## 📊 Permission Numerik yang Sering Digunakan

| Permission | Numeric | Arti | Penggunaan |
|---|---|---|---|
| rwx------ | 700 | Hanya owner | Folder pribadi user |
| rwxr-x--- | 750 | Owner + group baca/exec | Folder tim |
| rwxrwx--- | 770 | Owner + group full akses | Kolaborasi tim |
| rwxr-xr-x | 755 | Semua bisa baca/exec | Folder publik |
| rw-r--r-- | 644 | File publik (default) | File konfigurasi |
| rwxrwxrwt | 1777 | Sticky bit | Folder bersama (/tmp) |

---

# Bab VII: Checklist Keberhasilan

> ✅ **Checklist verifikasi** untuk memastikan semua konfigurasi berjalan dengan baik

## 👤 Manajemen User & Grup

| No | Item | Verifikasi | Status |
|---|---|---|---|
| 1 | 👤 User andi, siti, budi berhasil dibuat | `id andi` | ✅ |
| 2 | 🔐 Password untuk masing-masing user sudah diatur | `sudo passwd nama` | ✅ |
| 3 | 🆔 `id andi` menampilkan grup yang sesuai | `id andi` | ✅ |
| 4 | 🆔 `id budi` menampilkan primary group webdev | `id budi` | ✅ |
| 5 | 👥 Grup webdev dan project_alpha berhasil dibuat | `cat /etc/group` | ✅ |
| 6 | ➕ andi dan siti masuk ke grup webdev dan project_alpha (supplementary) | `id andi` | ✅ |
| 7 | 🔑 budi memiliki primary group webdev | `id budi` | ✅ |
| 8 | ❌ User tempuser berhasil dihapus (tanpa -r) | `cat /etc/passwd` | ✅ |
| 9 | ❌ User tempuser2 berhasil dihapus (dengan -r) | `cat /etc/passwd` | ✅ |
| 10 | ❌ Grup tempgroup berhasil dihapus setelah user dihapus | `cat /etc/group` | ✅ |

## 🔐 Hak Akses File

| No | Item | Verifikasi | Status |
|---|---|---|---|
| 1 | 📁 Folder /data/public dengan permission 755 | `ls -ld /data/public` | ✅ |
| 2 | 📁 Folder /data/team dengan permission 770, group project_alpha | `ls -ld /data/team` | ✅ |
| 3 | 📁 Folder /data/private/andi dan /data/private/siti dengan permission 700 | `ls -ld /data/private/*` | ✅ |
| 4 | 📁 Parent folder /data/private dengan permission 750 | `ls -ld /data/private` | ✅ |
| 5 | ✅ andi bisa akses folder sendiri, tidak bisa akses folder siti | `sudo su - andi` | ✅ |
| 6 | ✅ andi dan siti bisa akses /data/team | `sudo su - andi` | ✅ |
| 7 | ❌ budi tidak bisa akses /data/team | `sudo su - budi` | ✅ |

## 🛡️ Sticky Bit

| No | Item | Verifikasi | Status |
|---|---|---|---|
| 1 | 📁 Folder /data/backup memiliki sticky bit (drwxrwxrwt) | `ls -ld /data/backup` | ✅ |
| 2 | ✅ andi bisa membuat file di /data/backup | `sudo su - andi` | ✅ |
| 3 | ✅ siti bisa membuat file di /data/backup | `sudo su - siti` | ✅ |
| 4 | ❌ siti tidak bisa menghapus file milik andi | `sudo su - siti` | ✅ |
| 5 | ✅ siti bisa menghapus file milik sendiri | `sudo su - siti` | ✅ |

## 🗑️ Hapus User & Grup

| No | Item | Verifikasi | Status |
|---|---|---|---|
| 1 | ❌ User dihapus dengan userdel (tanpa -r) meninggalkan home directory | `ls -la /home/` | ✅ |
| 2 | ❌ User dihapus dengan userdel -r menghapus home directory | `ls -la /home/` | ✅ |
| 3 | ❌ Grup kosong bisa dihapus dengan groupdel | `cat /etc/group` | ✅ |
| 4 | ⚠️ Grup yang masih memiliki primary group tidak bisa dihapus | `sudo groupdel nama` | ✅ |

---

# Bab VIII: Catatan Praktikan

> 📝 *Catatan pribadi tentang kesulitan yang dihadapi dan pembelajaran penting selama praktikum.*

## 😟 Kesulitan yang Dihadapi

1. **Perbedaan adduser dan useradd** - adduser interaktif, useradd perlu opsi manual
2. **Primary vs Supplementary Group** - bingung dengan tampilan di /etc/group
3. **Permission numeric** - perlu konversi binary ke desimal untuk memahami
4. **Sticky bit** - huruf `t` di permission baru dipahami setelah praktik
5. **User dengan primary group tertentu** - harus buat grup dulu sebelum buat user
6. **Grup tidak bisa dihapus** - jika masih ada user dengan primary group tersebut
7. **Home directory tertinggal** - saat userdel tanpa opsi -r

## 🎓 Pembelajaran Penting

1. **User management** adalah fundamental skill untuk administrator sistem
2. **Grup** mempermudah pengelolaan permission untuk banyak user
3. **Permission rwx** sangat penting untuk keamanan file dan direktori
4. **Numeric permission** lebih cepat, symbolic lebih mudah dipahami
5. **Sticky bit** penting untuk folder bersama agar user tidak saling menghapus file
6. **Ownership** harus diatur dengan chown/chgrp untuk akses yang benar
7. **Cleanup user** harus hati-hati dengan opsi -r untuk menghapus home directory
8. **Testing permission** harus dilakukan dengan login sebagai user yang bersangkutan

---

# Bab IX: Referensi

> 📚 **Referensi dan dokumentasi** yang digunakan selama praktikum

## 🐧 Ubuntu Server Documentation
- **Website**: [https://ubuntu.com/server/docs](https://ubuntu.com/server/docs)
- **Topik**: User management, group management, file permissions

## 👤 User Management Commands
- **Website**: [https://manpages.ubuntu.com/manpages/](https://manpages.ubuntu.com/manpages/)
- **Topik**: useradd, usermod, userdel, adduser

## 👥 Group Management Commands
- **Website**: [https://manpages.ubuntu.com/manpages/](https://manpages.ubuntu.com/manpages/)
- **Topik**: groupadd, groupmod, groupdel

## 🔐 File Permissions
- **Website**: [https://wiki.archlinux.org/title/File_permissions_and_attributes](https://wiki.archlinux.org/title/File_permissions_and_attributes)
- **Topik**: chmod, chown, chgrp, sticky bit

## 🔍 Additional Resources

### 📖 **Linux User Management**
- `/etc/passwd` - User database
- `/etc/shadow` - Password database
- `/etc/group` - Group database
- `/etc/gshadow` - Group password database

### 🛡️ **Best Practices**
- Use `adduser` for interactive user creation
- Use `usermod -aG` to add user to supplementary groups
- Always test permissions with actual user login
- Use sticky bit for shared folders
- Clean up orphaned files after user deletion

---

> 🎯 **Kesimpulan**: Dokumentasi ini mencakup seluruh praktikum Manajemen User, Grup, dan Hak Akses File untuk praktikum Administrasi Sistem Pertemuan 3-4.

> 📅 **Terakhir diperbarui**: `[11/03/2026]`
> 👨‍💻 **Oleh**: `[Afrizal Ilza Munadhif]` - `[231240001420]`
> 🔗 **Repository**: [https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab](https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab)
