# Dokumentasi Praktikum Administrasi Sistem

> **Pertemuan 1–2: Instalasi dan Konfigurasi Dasar Ubuntu Server**
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
| 📅 Tanggal Praktik | `11/03/2026` |

# 📑 Daftar Isi

## 📑 Table of Contents

- [Bab I: Spesifikasi Virtual Machine](#bab-i-spesifikasi-virtual-machine)
  - [1.1 Konfigurasi Hardware VM](#11-konfigurasi-hardware-vm)
  - [1.2 Konfigurasi Network](#12-konfigurasi-network)
  - [1.3 Guest Additions](#13-instalasi-guest-additions)
- [Bab II: Konfigurasi Jaringan](#bab-ii-konfigurasi-jaringan)
  - [2.1 Identifikasi Interface Jaringan](#21-identifikasi-interface-jaringan)
  - [2.2 Konfigurasi IP Manual (Sementara)](#22-konfigurasi-ip-manual-sementara)
  - [2.3 Konfigurasi Routing Manual](#23-konfigurasi-routing-manual)
  - [2.4 Tes Koneksi](#24-tes-koneksi)
- [Bab III: Konfigurasi Netplan (Permanen)](#bab-iii-konfigurasi-netplan-permanen)
  - [3.1 Membuat File Netplan](#31-membuat-file-netplan)
  - [3.2 Isi File Netplan](#32-isi-file-netplan)
  - [3.3 Menyimpan File di Nano](#33-menyimpan-file-di-nano)
  - [3.4 Menerapkan Konfigurasi](#34-menerapkan-konfigurasi)
  - [3.5 Verifikasi Setelah Reboot](#35-verifikasi-setelah-reboot)
- [Bab IV: Konfigurasi SSH](#bab-iv-konfigurasi-ssh)
  - [4.1 Install dan Aktifkan SSH Server](#41-install-dan-aktifkan-ssh-server)
  - [4.2 Verifikasi SSH Listening](#42-verifikasi-ssh-listening)
  - [4.3 Konfigurasi SSH](#43-konfigurasi-ssh)
- [Bab V: Konfigurasi Windows Firewall](#bab-v-konfigurasi-windows-firewall)
  - [5.1 Membuat Aturan Firewall](#51-membuat-aturan-firewall-powershell)
  - [5.2 Verifikasi Aturan](#52-verifikasi-aturan)
- [Bab VI: Akses SSH dari Laptop](#bab-vi-akses-ssh-dari-laptop)
  - [6.1 Koneksi SSH](#61-koneksi-ssh)
  - [6.2 First Time Connection](#62-first-time-connection)
  - [6.3 Setelah Login Berhasil](#63-setelah-login-berhasil)
  - [6.4 SSH Verbose Mode](#64-ssh-verbose-mode)
  - [6.5 Keluar dari SSH](#65-keluar-dari-ssh)
- [Bab VII: Headless Mode](#bab-vii-headless-mode)
  - [7.1 Menjalankan VM Headless](#71-menjalankan-vm-headless)
  - [7.2 Keuntungan Headless Mode](#72-keuntungan-headless-mode)
  - [7.3 Mematikan VM](#73-mematikan-vm)
- [Bab VIII: Troubleshooting](#bab-viii-troubleshooting)
  - [8.1 Interface Tidak Muncul](#81-interface-tidak-muncul)
  - [8.2 Interface DOWN](#82-interface-down)
  - [8.3 Routing Salah](#83-routing-salah)
  - [8.4 SSH Timeout](#84-ssh-timeout)
- [Bab IX: Perbandingan Konfigurasi](#bab-ix-perbandingan-konfigurasi)
- [Bab X: Checklist Keberhasilan](#bab-x-checklist-keberhasilan)
- [Bab XI: Catatan Praktikan](#bab-xi-catatan-praktikan)
- [Bab XII: Referensi](#bab-xii-referensi)

---

# Bab I: Spesifikasi Virtual Machine

> 📖 *Pada bab ini akan dijelaskan spesifikasi hardware dan konfigurasi dasar Virtual Machine yang digunakan untuk praktikum.*

## 1.1 Konfigurasi Hardware VM

| Komponen | Spesifikasi |
|---|---|
| 💻 Nama VM | `ubuntu-server` |
| 🐧 OS Type | `Linux / Ubuntu 64-bit` |
| 🧠 RAM | `3072 MB (3 GB)` |
| ⚙️ CPU | `2 Core` |
| 💾 Disk | `25 GB (Dynamic Allocated)` |
| 🖥️ Video Memory | `16 MB` |

## 1.2 Konfigurasi Network

| Adapter | Tipe | Keterangan |
|---|---|---|
| 🌐 Adapter 1 | `NAT` | Untuk akses internet |
| 🔗 Adapter 2 | `Host-only` | Untuk koneksi ke laptop (192.168.56.x) |

> ⚠️ **Penting**: Opsi *Cable Connected* harus dicentang pada kedua adapter.

## 1.3 Instalasi Guest Additions

> 📋 **Catatan**: Guest Additions diperlukan untuk integrasi yang lebih baik antara host dan VM.

```bash
# Update package database
sudo apt update

# Install dependencies
sudo apt install build-essential dkms linux-headers-$(uname -r) -y

# Mount Guest Additions CD
sudo mount /dev/cdrom /mnt
cd /mnt

# Run installer
sudo ./VBoxLinuxAdditions.run

# Reboot system
sudo reboot
```

# Bab II: Konfigurasi Jaringan

> 🌐 *Pada bab ini akan dibahas konfigurasi jaringan dasar Ubuntu Server untuk memungkinkan komunikasi dengan host.*

## 2.1 Identifikasi Interface Jaringan

> 🔍 **Langkah 1**: Melihat semua interface jaringan

```bash
ip a
```

> 🔍 **Langkah 2**: Melihat interface tertentu

```bash
ip addr show enp0s3
ip addr show enp0s8
```

### 📝 Keterangan:
- `enp0s3` adalah interface **NAT** (IP biasanya `10.0.2.15`)
- `enp0s8` adalah interface **Host-only**

## 2.2 Konfigurasi IP Manual (Sementara)

> ⚙️ **Konfigurasi IP Address sementara** (akan hilang setelah reboot)

```bash
# Tambahkan IP address
sudo ip addr add 192.168.56.10/24 dev enp0s8

# Aktifkan interface
sudo ip link set enp0s8 up

# Verifikasi konfigurasi
ip addr show enp0s8
```

## 2.3 Konfigurasi Routing Manual

> 🛣️ **Konfigurasi routing** untuk menentukan jalur paket jaringan

```bash
# Tambahkan route untuk jaringan 192.168.56.0/24
sudo ip route add 192.168.56.0/24 dev enp0s8 src 192.168.56.10

# Verifikasi routing
ip route get 192.168.56.1
```

### ✅ **Hasil yang benar:**
```
192.168.56.1 dev enp0s8 src 192.168.56.10
```

## 2.4 Tes Koneksi

> 📡 **Testing koneksi** ke gateway untuk memastikan konfigurasi benar

```bash
# Ping ke gateway (4 paket)
ping -c 4 192.168.56.1
```

# Bab III: Konfigurasi Netplan (Permanen)

> 🔧 *Pada bab ini akan dibahas konfigurasi jaringan permanen menggunakan Netplan agar konfigurasi tetap tersimpan setelah reboot.*

## 3.1 Membuat File Netplan

> 📝 **Buat file konfigurasi Netplan** untuk konfigurasi jaringan permanen

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

## 3.2 Isi File Netplan

> 📋 **Isi file YAML** dengan konfigurasi jaringan yang diinginkan

```yaml
# Konfigurasi jaringan Netplan
network:
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      addresses:
        - 192.168.56.10/24
      routes:
        - to: 192.168.56.0/24
          via: 0.0.0.0
          scope: link
  version: 2
```

### ⚠️ **Catatan Penting:**
- Gunakan **spasi**, bukan tab
- Indentasi menggunakan **2 spasi**
- `version` harus sejajar dengan `ethernets`

## 3.3 Menyimpan File di Nano

> 💾 **Langkah menyimpan file** di editor Nano

| Langkah | Tombol | Keterangan |
|---|---|---|
| 🚪 Keluar dari editor | `Ctrl + X` | Keluar dari Nano |
| ✅ Konfirmasi simpan | `Y` | Yes untuk menyimpan |
| 📁 Konfirmasi nama file | `Enter` | Konfirmasi nama file |

## 3.4 Menerapkan Konfigurasi

> 🔄 **Menerapkan konfigurasi Netplan** dan restart sistem

```bash
# Set permission file
sudo chmod 600 /etc/netplan/00-installer-config.yaml

# Test konfigurasi (dry run)
sudo netplan try

# Apply konfigurasi
sudo netplan apply

# Reboot untuk memastikan konfigurasi tersimpan
sudo reboot
```

## 3.5 Verifikasi Setelah Reboot

> ✅ **Verifikasi konfigurasi** setelah sistem reboot

```bash
# Cek IP address interface enp0s8
ip addr show enp0s8

# Cek routing table
ip route get 192.168.56.1

# Test koneksi ke gateway
ping -c 4 192.168.56.1
```

# Bab IV: Konfigurasi SSH

> 🔐 *Pada bab ini akan dibahas instalasi dan konfigurasi SSH Server untuk remote access ke Ubuntu Server.*

## 4.1 Install dan Aktifkan SSH Server

> 📦 **Instalasi OpenSSH Server** dan konfigurasi service

```bash
# Update package database
sudo apt update

# Install OpenSSH Server
sudo apt install openssh-server -y

# Start SSH service
sudo systemctl start ssh

# Enable SSH service (auto-start on boot)
sudo systemctl enable ssh

# Check SSH service status
sudo systemctl status ssh
```
## 4.2 Verifikasi SSH Listening

> 🔍 **Verifikasi bahwa SSH** berjalan dan listening di port 22

```bash
# Cek port yang listening
sudo ss -tulpn | grep :22
```

### ✅ **Output yang benar:**
```
0.0.0.0:22
[::]:22
```

## 4.3 Konfigurasi SSH

> ⚙️ **Edit file konfigurasi SSH** untuk keamanan dan aksesibilitas

```bash
# Edit file konfigurasi SSH
sudo nano /etc/ssh/sshd_config
```

### 📋 **Pastikan konfigurasi berikut:**

```bash
# Port SSH (default: 22)
Port 22

# Listen address (0.0.0.0 untuk semua interface)
ListenAddress 0.0.0.0

# Disable root login untuk keamanan
PermitRootLogin no

# Enable password authentication
PasswordAuthentication yes
```

> 🔄 **Restart SSH service** untuk menerapkan perubahan:

```bash
sudo systemctl restart ssh
```

# Bab V: Konfigurasi Windows Firewall

> 🛡️ *Pada bab ini akan dibahas konfigurasi Windows Firewall untuk memungkinkan koneksi SSH dari laptop ke VM.*

## 5.1 Membuat Aturan Firewall (PowerShell)

> 🔥 **Buat aturan inbound** untuk port SSH (22) di Windows Firewall

```powershell
# Buat rule baru untuk SSH
New-NetFirewallRule -DisplayName "SSH ke VM" -Direction Inbound -Protocol TCP -LocalPort 22 -Action Allow
```

### 📝 **Keterangan:**
- `-DisplayName`: Nama aturan yang akan muncul di Windows Firewall
- `-Direction Inbound`: Arah koneksi masuk
- `-Protocol TCP`: Protokol yang digunakan SSH
- `-LocalPort 22`: Port SSH
- `-Action Allow`: Izinkan koneksi

## 5.2 Verifikasi Aturan

> ✅ **Verifikasi aturan firewall** yang telah dibuat

```powershell
# Tampilkan detail rule yang dibuat
Get-NetFirewallRule -DisplayName "SSH ke VM" | Format-List
```

### 🔍 **Alternative: Verifikasi melalui GUI**
1. Buka **Windows Defender Firewall with Advanced Security**
2. Navigasi ke **Inbound Rules**
3. Cari rule dengan nama "SSH ke VM"
4. Pastikan status **Enabled**

# Bab VI: Akses SSH dari Laptop

> 💻 *Pada bab ini akan dibahas cara mengakses Ubuntu Server dari laptop menggunakan SSH client.*

## 6.1 Koneksi SSH

> 🔗 **Koneksi ke VM** menggunakan SSH dari terminal laptop

```bash
# Koneksi SSH ke VM
ssh gix@192.168.56.10
```

### 📝 **Format koneksi:**
- `ssh`: Command SSH client
- `gix`: Username di VM
- `192.168.56.10`: IP address VM

## 6.2 First Time Connection

> 🆕 **Koneksi pertama kali** akan menampilkan pesan verifikasi host key

Saat pertama kali terhubung akan muncul pesan verifikasi host:

```
The authenticity of host '192.168.56.10 (192.168.56.10)' can't be established.
ECDSA key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

### ✅ **Jawab dengan:**
```bash
yes
```

### 🔐 **Masukkan password:**
```
gix@192.168.56.10's password: Unisnu2024!
```

## 6.3 Setelah Login Berhasil

> 🎉 **Tampilan terminal** setelah berhasil login ke VM

```bash
gix@ubuntu-server:~$
```

### 📊 **Informasi yang ditampilkan:**
- `gix`: Username yang sedang login
- `ubuntu-server`: Hostname VM
- `~`: Home directory user
- `$`: Prompt untuk user biasa (bukan root)

## 6.4 SSH Verbose Mode

> 🔍 **Mode verbose** untuk troubleshooting koneksi SSH

```bash
# Verbose mode dengan detail maksimal
ssh -vvv gix@192.168.56.10
```

### 📋 **Level verbose:**
- `-v`: Verbose mode (basic)
- `-vv`: Verbose mode (verbose)
- `-vvv`: Verbose mode (verbose maksimal)

## 6.5 Keluar dari SSH

> 🚪 **Cara keluar** dari sesi SSH dengan aman

### 🔄 **Metode 1: Command exit**
```bash
exit
```

### 🔄 **Metode 2: Command logout**
```bash
logout
```

### ⌨️ **Metode 3: Shortcut keyboard**
Tekan `Ctrl + D`

# Bab VII: Headless Mode

> 🖥️ *Pada bab ini akan dibahas cara menjalankan VM tanpa tampilan GUI (headless) untuk menghemat resource.*

## 7.1 Menjalankan VM Headless

> 🚀 **Menjalankan VM** tanpa tampilan window menggunakan command line

### 📋 **Langkah 1: List VM yang tersedia**
```bash
# Tampilkan semua VM yang tersedia
VBoxManage list vms
```

### 🚀 **Langkah 2: Start VM dalam mode headless**
```bash
# Jalankan VM tanpa tampilan GUI
VBoxManage startvm "ubuntu-server" --type headless
```

### 📝 **Keterangan:**
- `VBoxManage`: Command line management tool VirtualBox
- `startvm`: Perintah untuk menjalankan VM
- `"ubuntu-server"`: Nama VM
- `--type headless`: Mode tanpa tampilan GUI
## 7.2 Keuntungan Headless Mode

> 💡 **Keuntungan menggunakan** headless mode untuk VM

| Aspek | Normal Mode | Headless Mode |
|---|---|---|
| 🖥️ Jendela VM | Muncul | Tidak muncul |
| 🧠 Resource RAM | Lebih banyak | Lebih hemat |
| 🔗 Akses | VM dan SSH | SSH saja |
| ⚡ Performa | Lebih lambat | Lebih cepat |
| 💾 Resource GPU | Digunakan | Tidak digunakan |

## 7.3 Mematikan VM

> 🔌 **Cara mematikan VM** yang berjalan dalam mode headless

### 🔄 **Metode 1: Melalui SSH**
```bash
# Shutdown dari dalam VM via SSH
sudo shutdown now
```

### 🔄 **Metode 2: Melalui VBoxManage**
```bash
# Shutdown dari host machine
VBoxManage controlvm "ubuntu-server" poweroff
```

### 🔄 **Metode 3: Graceful shutdown**
```bash
# Shutdown dengan signal ACPI
VBoxManage controlvm "ubuntu-server" acpipowerbutton
```

# Bab VIII: Troubleshooting

> 🛠️ *Pada bab ini akan dibahas masalah-masalah umum yang sering terjadi dan solusinya.*

## 8.1 Interface Tidak Muncul

> ❌ **Masalah**: Interface `enp0s8` tidak muncul saat dicek dengan `ip a`

### 🔍 **Penyebab:**
Adapter 2 tidak aktif di VirtualBox.

### ✅ **Solusi:**
1. Buka **VirtualBox Manager**
2. Pilih VM `ubuntu-server`
3. Klik **Settings** → **Network**
4. Pilih **Adapter 2**
5. Centang **Enable Network Adapter**
6. Centang **Cable Connected**
7. Klik **OK**
8. Restart VM

## 8.2 Interface DOWN

> ⬇️ **Masalah**: Interface `enp0s8` muncul tapi status DOWN

### 🔧 **Solusi:**
```bash
# Aktifkan interface enp0s8
sudo ip link set enp0s8 up

# Verifikasi status
ip link show enp0s8
```

## 8.3 Routing Salah

> 🛣️ **Masalah**: Routing tidak benar, tidak bisa koneksi ke network lain

### 🔧 **Solusi:**
```bash
# Hapus routing yang salah
sudo ip route del 192.168.56.0/24

# Tambahkan routing yang benar
sudo ip route add 192.168.56.0/24 dev enp0s8 src 192.168.56.10

# Verifikasi routing
ip route show
```

## 8.4 SSH Timeout

> ⏰ **Masalah**: Koneksi SSH timeout atau connection refused

### 🔍 **Penyebab:**
Windows Firewall memblokir port 22.

### ✅ **Solusi:**
1. **Buat rule inbound port 22** (lihat Bab V)
2. **Verifikasi rule aktif**:
   ```powershell
   Get-NetFirewallRule -DisplayName "SSH ke VM"
   ```
3. **Test koneksi dengan telnet**:
   ```bash
   telnet 192.168.56.10 22
   ```

# Bab IX: Perbandingan Konfigurasi

> 📊 *Perbandingan antara konfigurasi sementara dan permanen untuk jaringan.*

| Komponen | Sementara | Permanen |
|---|---|---|
| 🌐 IP Address | `ip addr add` | `Netplan` |
| 🛣️ Routing | `ip route add` | `Netplan` |
| 🔄 Bertahan setelah reboot | ❌ Tidak | ✅ Ya |
| ⚡ Kecepatan setup | 🚀 Cepat | 🐢 Lebih lama |
| 📝 Kompleksitas | 🟢 Mudah | 🟡 Sedang |
| 🔧 Maintenance | 🔄 Harus diulang | 📂 Otomatis |

# Bab X: Checklist Keberhasilan

> ✅ **Checklist verifikasi** untuk memastikan semua konfigurasi berjalan dengan baik

| No | Item | Verifikasi | Status |
|---|---|---|---|
| 1 | 🖥️ VM berhasil boot | `OK` | ✅ |
| 2 | 👤 Login user | `whoami` | ✅ |
| 3 | 🌐 IP enp0s8 aktif | `ip addr` | ✅ |
| 4 | 🛣️ Routing benar | `ip route` | ✅ |
| 5 | 📡 Ping ke host | `ping` | ✅ |
| 6 | 🔐 SSH aktif | `systemctl status ssh` | ✅ |
| 7 | 🔌 SSH listening | `ss -tulpn` | ✅ |
| 8 | 🛡️ Firewall rule | PowerShell | ✅ |
| 9 | 💻 Koneksi SSH | `ssh gix@192.168.56.10` | ✅ |

# Bab XI: Catatan Praktikan

> 📝 *Catatan pribadi tentang kesulitan yang dihadapi dan pembelajaran penting selama praktikum.*

## 😟 Kesulitan yang Dihadapi

1. **Interface enp0s8 tidak muncul** karena adapter belum diaktifkan di VirtualBox
2. **Routing salah** karena belum menambahkan route manual untuk jaringan 192.168.56.0/24
3. **SSH timeout** karena Windows Firewall memblokir port 22 di laptop
4. **Guest Additions tidak aktif** sampai dilakukan reboot kedua setelah instalasi
5. **Error netplan** akibat indentasi YAML yang salah menggunakan tab bukan spasi
6. **SSH listen di localhost** karena konfigurasi `ListenAddress` tidak di-set ke `0.0.0.0`
7. **File netplan tidak ada** karena salah path atau permission issue

## 🎓 Pembelajaran Penting

1. **Host-only adapter** membuat jaringan privat antara host dan VM dengan subnet terpisah
2. **Routing** menentukan jalur paket jaringan dan sangat penting untuk komunikasi antar network
3. **Firewall** harus dikonfigurasi pada kedua sisi (host dan VM) untuk koneksi yang berhasil
4. **SSH** merupakan alat utama administrator sistem untuk remote management
5. **Netplan** digunakan untuk konfigurasi jaringan permanen di Ubuntu dengan format YAML
6. **Indentasi YAML** sangat kritis - harus menggunakan spasi, bukan tab
7. **Headless mode** sangat berguna untuk menghemat resource saat VM tidak perlu GUI
8. **Troubleshooting** sistematis dimulai dari layer fisik (adapter) hingga application (SSH)

# Bab XII: Referensi

> 📚 **Referensi dan dokumentasi** yang digunakan selama praktikum

## 🐧 Ubuntu Server Documentation
- **Website**: [https://ubuntu.com/server/docs](https://ubuntu.com/server/docs)
- **Topik**: Instalasi, konfigurasi dasar, networking

## 🔧 Netplan Documentation
- **Website**: [https://netplan.io/documentation](https://netplan.io/documentation)
- **Topik**: Konfigurasi jaringan permanen, format YAML

## 🔐 OpenSSH Manual
- **Website**: [https://www.openssh.com/manual.html](https://www.openssh.com/manual.html)
- **Topik**: SSH configuration, security, troubleshooting

## 🖥️ VirtualBox Documentation
- **Website**: [https://www.virtualbox.org/wiki/Documentation](https://www.virtualbox.org/wiki/Documentation)
- **Topik**: Virtual machine management, networking, headless mode

## 🔍 Additional Resources

### 📖 **Linux Networking Commands**
- `ip` - Modern replacement for ifconfig
- `ss` - Socket statistics replacement for netstat
- `ping` - Network connectivity testing
- `traceroute` - Route tracing utility

### 🛡️ **Security Best Practices**
- Disable root login via SSH
- Use key-based authentication when possible
- Regular system updates
- Firewall configuration on both sides

---

> 🎯 **Kesimpulan**: Dokumentasi ini mencakup seluruh proses instalasi dan konfigurasi Ubuntu Server untuk praktikum Administrasi Sistem, mulai dari setup VM hingga remote access via SSH.

> 📅 **Terakhir diperbarui**: `[11/03/2026]`
> 👨‍💻 **Oleh**: `[Afrizal Ilza Munadhif]` - `[231240001420]`
> 🔗 **Repository**: [https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab](https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab)
