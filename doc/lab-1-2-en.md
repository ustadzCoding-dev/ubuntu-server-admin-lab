# System Administration Lab Documentation

> **Sessions 1–2: Ubuntu Server Basic Installation and Configuration**
> *Operating System: Ubuntu Server 22.04 LTS*

---

# 📋 Practitioner Identity

| Identity | Information |
|---|---|
| 📝 Name | `Afrizal Ilza Munadhif` |
| 🆔 Student ID | `231240001420` |
| 👤 VM Username | `gix` |
| 🔐 Password | `******` |
| 🌐 VM IP | `192.168.56.10` |
| 🖥️ Hostname | `ubuntu-server` |
| 📅 Practice Date | `11/03/2026` |

# 📑 Table of Contents

## 📑 Complete Table of Contents

- [Chapter I: Virtual Machine Specifications](#chapter-i-virtual-machine-specifications)
  - [1.1 VM Hardware Configuration](#11-vm-hardware-configuration)
  - [1.2 Network Configuration](#12-network-configuration)
  - [1.3 Guest Additions](#13-guest-additions-installation)
- [Chapter II: Network Configuration](#chapter-ii-network-configuration)
  - [2.1 Network Interface Identification](#21-network-interface-identification)
  - [2.2 Manual IP Configuration (Temporary)](#22-manual-ip-configuration-temporary)
  - [2.3 Manual Routing Configuration](#23-manual-routing-configuration)
  - [2.4 Connection Test](#24-connection-test)
- [Chapter III: Netplan Configuration (Permanent)](#chapter-iii-netplan-configuration-permanent)
  - [3.1 Creating Netplan File](#31-creating-netplan-file)
  - [3.2 Netplan File Content](#32-netplan-file-content)
  - [3.3 Saving File in Nano](#33-saving-file-in-nano)
  - [3.4 Applying Configuration](#34-applying-configuration)
  - [3.5 Verification After Reboot](#35-verification-after-reboot)
- [Chapter IV: SSH Configuration](#chapter-iv-ssh-configuration)
  - [4.1 Install and Enable SSH Server](#41-install-and-enable-ssh-server)
  - [4.2 Verify SSH Listening](#42-verify-ssh-listening)
  - [4.3 SSH Configuration](#43-ssh-configuration)
- [Chapter V: Windows Firewall Configuration](#chapter-v-windows-firewall-configuration)
  - [5.1 Creating Firewall Rule](#51-creating-firewall-rule-powershell)
  - [5.2 Verifying Rule](#52-verifying-rule)
- [Chapter VI: SSH Access from Laptop](#chapter-vi-ssh-access-from-laptop)
  - [6.1 SSH Connection](#61-ssh-connection)
  - [6.2 First Time Connection](#62-first-time-connection)
  - [6.3 After Successful Login](#63-after-successful-login)
  - [6.4 SSH Verbose Mode](#64-ssh-verbose-mode)
  - [6.5 Exiting SSH](#65-exiting-ssh)
- [Chapter VII: Headless Mode](#chapter-vii-headless-mode)
  - [7.1 Running VM Headless](#71-running-vm-headless)
  - [7.2 Headless Mode Benefits](#72-headless-mode-benefits)
  - [7.3 Shutting Down VM](#73-shutting-down-vm)
- [Chapter VIII: Troubleshooting](#chapter-viii-troubleshooting)
  - [8.1 Interface Not Appearing](#81-interface-not-appearing)
  - [8.2 Interface DOWN](#82-interface-down)
  - [8.3 Wrong Routing](#83-wrong-routing)
  - [8.4 SSH Timeout](#84-ssh-timeout)
- [Chapter IX: Configuration Comparison](#chapter-ix-configuration-comparison)
- [Chapter X: Success Checklist](#chapter-x-success-checklist)
- [Chapter XI: Practitioner Notes](#chapter-xi-practitioner-notes)
- [Chapter XII: References](#chapter-xii-references)

---

# Chapter I: Virtual Machine Specifications

> 📖 *This chapter explains the hardware specifications and basic Virtual Machine configuration used for the practicum.*

## 1.1 VM Hardware Configuration

| Component | Specification |
|---|---|
| 💻 VM Name | `ubuntu-server` |
| 🐧 OS Type | `Linux / Ubuntu 64-bit` |
| 🧠 RAM | `3072 MB (3 GB)` |
| ⚙️ CPU | `2 Core` |
| 💾 Disk | `25 GB (Dynamic Allocated)` |
| 🖥️ Video Memory | `16 MB` |

## 1.2 Network Configuration

| Adapter | Type | Description |
|---|---|---|
| 🌐 Adapter 1 | `NAT` | For internet access |
| 🔗 Adapter 2 | `Host-only` | For laptop connection (192.168.56.x) |

> ⚠️ **Important**: *Cable Connected* option must be checked on both adapters.

## 1.3 Guest Additions Installation

> 📋 **Note**: Guest Additions are required for better integration between host and VM.

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

# Chapter II: Network Configuration

> 🌐 *This chapter covers basic Ubuntu Server network configuration to enable communication with the host.*

## 2.1 Network Interface Identification

> 🔍 **Step 1**: View all network interfaces

```bash
ip a
```

> 🔍 **Step 2**: View specific interface

```bash
ip addr show enp0s3
ip addr show enp0s8
```

### 📝 Description:
- `enp0s3` is the **NAT** interface (IP usually `10.0.2.15`)
- `enp0s8` is the **Host-only** interface

## 2.2 Manual IP Configuration (Temporary)

> ⚙️ **Temporary IP Address configuration** (will be lost after reboot)

```bash
# Add IP address
sudo ip addr add 192.168.56.10/24 dev enp0s8

# Enable interface
sudo ip link set enp0s8 up

# Verify configuration
ip addr show enp0s8
```

## 2.3 Manual Routing Configuration

> 🛣️ **Routing configuration** to determine network packet paths

```bash
# Add route for network 192.168.56.0/24
sudo ip route add 192.168.56.0/24 dev enp0s8 src 192.168.56.10

# Verify routing
ip route get 192.168.56.1
```

### ✅ **Correct result:**
```
192.168.56.1 dev enp0s8 src 192.168.56.10
```

## 2.4 Connection Test

> 📡 **Connection testing** to gateway to ensure correct configuration

```bash
# Ping to gateway (4 packets)
ping -c 4 192.168.56.1
```

# Chapter III: Netplan Configuration (Permanent)

> 🔧 *This chapter covers permanent network configuration using Netplan so settings persist after reboot.*

## 3.1 Creating Netplan File

> 📝 **Create Netplan configuration file** for permanent network configuration

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

## 3.2 Netplan File Content

> 📋 **Fill the YAML file** with desired network configuration

```yaml
# Netplan network configuration
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

### ⚠️ **Important Notes:**
- Use **spaces**, not tabs
- Indentation uses **2 spaces**
- `version` must be aligned with `ethernets`

## 3.3 Saving File in Nano

> 💾 **Steps to save file** in Nano editor

| Step | Key | Description |
|---|---|---|
| 🚪 Exit editor | `Ctrl + X` | Exit Nano |
| ✅ Confirm save | `Y` | Yes to save |
| 📁 Confirm filename | `Enter` | Confirm filename |

## 3.4 Applying Configuration

> 🔄 **Applying Netplan configuration** and restarting system

```bash
# Set file permission
sudo chmod 600 /etc/netplan/00-installer-config.yaml

# Test configuration (dry run)
sudo netplan try

# Apply configuration
sudo netplan apply

# Reboot to ensure configuration persists
sudo reboot
```

## 3.5 Verification After Reboot

> ✅ **Verify configuration** after system reboot

```bash
# Check enp0s8 interface IP address
ip addr show enp0s8

# Check routing table
ip route get 192.168.56.1

# Test connection to gateway
ping -c 4 192.168.56.1
```

# Chapter IV: SSH Configuration

> 🔐 *This chapter covers SSH Server installation and configuration for remote access to Ubuntu Server.*

## 4.1 Install and Enable SSH Server

> 📦 **OpenSSH Server installation** and service configuration

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

## 4.2 Verify SSH Listening

> 🔍 **Verify that SSH** is running and listening on port 22

```bash
# Check listening ports
sudo ss -tulpn | grep :22
```

### ✅ **Correct output:**
```
0.0.0.0:22
[::]:22
```

## 4.3 SSH Configuration

> ⚙️ **Edit SSH configuration file** for security and accessibility

```bash
# Edit SSH configuration file
sudo nano /etc/ssh/sshd_config
```

### 📋 **Ensure the following configuration:**

```bash
# SSH Port (default: 22)
Port 22

# Listen address (0.0.0.0 for all interfaces)
ListenAddress 0.0.0.0

# Disable root login for security
PermitRootLogin no

# Enable password authentication
PasswordAuthentication yes
```

> 🔄 **Restart SSH service** to apply changes:

```bash
sudo systemctl restart ssh
```

# Chapter V: Windows Firewall Configuration

> 🛡️ *This chapter covers Windows Firewall configuration to enable SSH connection from laptop to VM.*

## 5.1 Creating Firewall Rule (PowerShell)

> 🔥 **Create inbound rule** for SSH port (22) in Windows Firewall

```powershell
# Create new rule for SSH
New-NetFirewallRule -DisplayName "SSH to VM" -Direction Inbound -Protocol TCP -LocalPort 22 -Action Allow
```

### 📝 **Description:**
- `-DisplayName`: Rule name that will appear in Windows Firewall
- `-Direction Inbound`: Incoming connection direction
- `-Protocol TCP`: Protocol used by SSH
- `-LocalPort 22`: SSH port
- `-Action Allow`: Allow connection

## 5.2 Verifying Rule

> ✅ **Verify firewall rule** that was created

```powershell
# Display details of created rule
Get-NetFirewallRule -DisplayName "SSH to VM" | Format-List
```

### 🔍 **Alternative: Verify via GUI**
1. Open **Windows Defender Firewall with Advanced Security**
2. Navigate to **Inbound Rules**
3. Find rule named "SSH to VM"
4. Ensure status is **Enabled**

# Chapter VI: SSH Access from Laptop

> 💻 *This chapter covers how to access Ubuntu Server from laptop using SSH client.*

## 6.1 SSH Connection

> 🔗 **Connect to VM** using SSH from laptop terminal

```bash
# SSH connection to VM
ssh gix@192.168.56.10
```

### 📝 **Connection format:**
- `ssh`: SSH client command
- `gix`: Username in VM
- `192.168.56.10`: VM IP address

## 6.2 First Time Connection

> 🆕 **First time connection** will display host key verification message

When connecting for the first time, a host verification message will appear:

```
The authenticity of host '192.168.56.10 (192.168.56.10)' can't be established.
ECDSA key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

### ✅ **Answer with:**
```bash
yes
```

### 🔐 **Enter password:**
```
gix@192.168.56.10's password: Unisnu2024!
```

## 6.3 After Successful Login

> 🎉 **Terminal display** after successfully logging into VM

```bash
gix@ubuntu-server:~$
```

### 📊 **Displayed information:**
- `gix`: Currently logged-in username
- `ubuntu-server`: VM hostname
- `~`: User home directory
- `$`: Prompt for regular user (not root)

## 6.4 SSH Verbose Mode

> 🔍 **Verbose mode** for troubleshooting SSH connection

```bash
# Verbose mode with maximum detail
ssh -vvv gix@192.168.56.10
```

### 📋 **Verbose levels:**
- `-v`: Verbose mode (basic)
- `-vv`: Verbose mode (verbose)
- `-vvv`: Verbose mode (maximum verbose)

## 6.5 Exiting SSH

> 🚪 **Safe ways to exit** SSH session

### 🔄 **Method 1: exit command**
```bash
exit
```

### 🔄 **Method 2: logout command**
```bash
logout
```

### ⌨️ **Method 3: Keyboard shortcut**
Press `Ctrl + D`

# Chapter VII: Headless Mode

> 🖥️ *This chapter covers running VM without GUI display (headless) to save resources.*

## 7.1 Running VM Headless

> 🚀 **Run VM** without window display using command line

### 📋 **Step 1: List available VMs**
```bash
# Display all available VMs
VBoxManage list vms
```

### 🚀 **Step 2: Start VM in headless mode**
```bash
# Run VM without GUI display
VBoxManage startvm "ubuntu-server" --type headless
```

### 📝 **Description:**
- `VBoxManage`: VirtualBox command line management tool
- `startvm`: Command to start VM
- `"ubuntu-server"`: VM name
- `--type headless`: Mode without GUI display

## 7.2 Headless Mode Benefits

> 💡 **Benefits of using** headless mode for VM

| Aspect | Normal Mode | Headless Mode |
|---|---|---|
| 🖥️ VM Window | Appears | Does not appear |
| 🧠 RAM Resource | More | More efficient |
| 🔗 Access | VM and SSH | SSH only |
| ⚡ Performance | Slower | Faster |
| 💾 GPU Resource | Used | Not used |

## 7.3 Shutting Down VM

> 🔌 **How to shutdown VM** running in headless mode

### 🔄 **Method 1: Via SSH**
```bash
# Shutdown from within VM via SSH
sudo shutdown now
```

### 🔄 **Method 2: Via VBoxManage**
```bash
# Shutdown from host machine
VBoxManage controlvm "ubuntu-server" poweroff
```

### 🔄 **Method 3: Graceful shutdown**
```bash
# Shutdown with ACPI signal
VBoxManage controlvm "ubuntu-server" acpipowerbutton
```

# Chapter VIII: Troubleshooting

> 🛠️ *This chapter covers common problems that often occur and their solutions.*

## 8.1 Interface Not Appearing

> ❌ **Problem**: Interface `enp0s8` does not appear when checked with `ip a`

### 🔍 **Cause:**
Adapter 2 is not active in VirtualBox.

### ✅ **Solution:**
1. Open **VirtualBox Manager**
2. Select VM `ubuntu-server`
3. Click **Settings** → **Network**
4. Select **Adapter 2**
5. Check **Enable Network Adapter**
6. Check **Cable Connected**
7. Click **OK**
8. Restart VM

## 8.2 Interface DOWN

> ⬇️ **Problem**: Interface `enp0s8` appears but status is DOWN

### 🔧 **Solution:**
```bash
# Enable enp0s8 interface
sudo ip link set enp0s8 up

# Verify status
ip link show enp0s8
```

## 8.3 Wrong Routing

> 🛣️ **Problem**: Routing is incorrect, cannot connect to other networks

### 🔧 **Solution:**
```bash
# Delete wrong routing
sudo ip route del 192.168.56.0/24

# Add correct routing
sudo ip route add 192.168.56.0/24 dev enp0s8 src 192.168.56.10

# Verify routing
ip route show
```

## 8.4 SSH Timeout

> ⏰ **Problem**: SSH connection timeout or connection refused

### 🔍 **Cause:**
Windows Firewall is blocking port 22.

### ✅ **Solution:**
1. **Create inbound port 22 rule** (see Chapter V)
2. **Verify rule is active**:
   ```powershell
   Get-NetFirewallRule -DisplayName "SSH to VM"
   ```
3. **Test connection with telnet**:
   ```bash
   telnet 192.168.56.10 22
   ```

# Chapter IX: Configuration Comparison

> 📊 *Comparison between temporary and permanent configuration for networking.*

| Component | Temporary | Permanent |
|---|---|---|
| 🌐 IP Address | `ip addr add` | `Netplan` |
| 🛣️ Routing | `ip route add` | `Netplan` |
| 🔄 Persist after reboot | ❌ No | ✅ Yes |
| ⚡ Setup speed | 🚀 Fast | 🐢 Longer |
| 📝 Complexity | 🟢 Easy | 🟡 Medium |
| 🔧 Maintenance | 🔄 Must repeat | 📂 Automatic |

# Chapter X: Success Checklist

> ✅ **Verification checklist** to ensure all configurations run properly

| No | Item | Verification | Status |
|---|---|---|---|
| 1 | 🖥️ VM successfully boots | `OK` | ✅ |
| 2 | 👤 User login | `whoami` | ✅ |
| 3 | 🌐 IP enp0s8 active | `ip addr` | ✅ |
| 4 | 🛣️ Correct routing | `ip route` | ✅ |
| 5 | 📡 Ping to host | `ping` | ✅ |
| 6 | 🔐 SSH active | `systemctl status ssh` | ✅ |
| 7 | 🔌 SSH listening | `ss -tulpn` | ✅ |
| 8 | 🛡️ Firewall rule | PowerShell | ✅ |
| 9 | 💻 SSH connection | `ssh gix@192.168.56.10` | ✅ |

# Chapter XI: Practitioner Notes

> 📝 *Personal notes about difficulties encountered and important learnings during the practicum.*

## 😟 Difficulties Encountered

1. **Interface enp0s8 did not appear** because adapter was not enabled in VirtualBox
2. **Wrong routing** because manual route for network 192.168.56.0/24 was not added
3. **SSH timeout** because Windows Firewall blocked port 22 on laptop
4. **Guest Additions not active** until second reboot after installation
5. **Netplan error** due to wrong YAML indentation using tab instead of spaces
6. **SSH listen on localhost** because `ListenAddress` configuration was not set to `0.0.0.0`
7. **Netplan file not found** due to wrong path or permission issue

## 🎓 Important Learnings

1. **Host-only adapter** creates a private network between host and VM with separate subnet
2. **Routing** determines network packet paths and is crucial for inter-network communication
3. **Firewall** must be configured on both sides (host and VM) for successful connection
4. **SSH** is the main administrator tool for remote management
5. **Netplan** is used for permanent network configuration in Ubuntu with YAML format
6. **YAML indentation** is critical - must use spaces, not tabs
7. **Headless mode** is useful for saving resources when VM doesn't need GUI
8. **Troubleshooting** systematically starts from physical layer (adapter) to application layer (SSH)

# Chapter XII: References

> 📚 **References and documentation** used during the practicum

## 🐧 Ubuntu Server Documentation
- **Website**: [https://ubuntu.com/server/docs](https://ubuntu.com/server/docs)
- **Topics**: Installation, basic configuration, networking

## 🔧 Netplan Documentation
- **Website**: [https://netplan.io/documentation](https://netplan.io/documentation)
- **Topics**: Permanent network configuration, YAML format

## 🔐 OpenSSH Manual
- **Website**: [https://www.openssh.com/manual.html](https://www.openssh.com/manual.html)
- **Topics**: SSH configuration, security, troubleshooting

## 🖥️ VirtualBox Documentation
- **Website**: [https://www.virtualbox.org/wiki/Documentation](https://www.virtualbox.org/wiki/Documentation)
- **Topics**: Virtual machine management, networking, headless mode

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

> 🎯 **Conclusion**: This documentation covers the entire Ubuntu Server installation and configuration process for the System Administration practicum, from VM setup to remote access via SSH.

> 📅 **Last updated**: `[11/03/2026]`
> 👨‍💻 **By**: `[Afrizal Ilza Munadhif]` - `[231240001420]`
> 🔗 **Repository**: [https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab](https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab)
