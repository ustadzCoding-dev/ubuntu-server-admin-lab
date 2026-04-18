# System Administration Lab Documentation

> **Session 5: System Security (Firewall, SSH Hardening, Key Authentication, Monitoring)**
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
| 📅 Practice Date | `19/04/2026` |

# 📑 Table of Contents

- [Chapter I: Basic Firewall (UFW)](#chapter-i-basic-firewall-ufw)
  - [1.1 Check Firewall Status](#11-check-firewall-status)
  - [1.2 View Default Rules](#12-view-default-rules)
  - [1.3 Allow SSH](#13-allow-ssh)
  - [1.4 Enable Firewall](#14-enable-firewall)
  - [1.5 Allow Other Ports](#15-allow-other-ports)
  - [1.6 View Rules with Numbers](#16-view-rules-with-numbers)
  - [1.7 Delete Rules](#17-delete-rules)
  - [1.8 Disable Firewall](#18-disable-firewall)
- [Chapter II: SSH Hardening](#chapter-ii-ssh-hardening)
  - [2.1 Backup SSH Configuration](#21-backup-ssh-configuration)
  - [2.2 Change SSH Port](#22-change-ssh-port)
  - [2.3 Allow New Port in Firewall](#23-allow-new-port-in-firewall)
  - [2.4 Disable Root Login via SSH](#24-disable-root-login-via-ssh)
  - [2.5 Limit Users Allowed to Login via SSH](#25-limit-users-allowed-to-login-via-ssh)
  - [2.6 Restart SSH and Verify](#26-restart-ssh-and-verify)
- [Chapter III: SSH Key Authentication (Passwordless Login)](#chapter-iii-ssh-key-authentication-passwordless-login)
  - [3.1 Generate Key Pair on Laptop](#31-generate-key-pair-on-laptop)
  - [3.2 Copy Public Key to Server](#32-copy-public-key-to-server)
  - [3.3 Set Private Key Permissions (Windows)](#33-set-private-key-permissions-windows)
  - [3.4 Test Passwordless Login](#34-test-passwordless-login)
  - [3.5 Disable Password Authentication](#35-disable-password-authentication)
- [Chapter IV: Basic Security Monitoring](#chapter-iv-basic-security-monitoring)
  - [4.1 View Authentication Log](#41-view-authentication-log)
  - [4.2 Find Failed Login Attempts](#42-find-failed-login-attempts)
  - [4.3 View User Login History](#43-view-user-login-history)
  - [4.4 Active Monitoring with journalctl](#44-active-monitoring-with-journalctl)
- [Chapter V: Important Commands Summary](#chapter-v-important-commands-summary)
- [Chapter VI: Success Checklist](#chapter-vi-success-checklist)
- [Chapter VII: Conclusion](#chapter-vii-conclusion)
- [Chapter VIII: References](#chapter-viii-references)

---

# Chapter I: Basic Firewall (UFW)

> 📖 *This chapter explains how to enable and manage firewall using UFW (Uncomplicated Firewall) on Ubuntu Server.*

## 1.1 Check Firewall Status

> 🔍 **Step 1**: Check current firewall status

```bash
sudo ufw status
```

### ✅ **Initial output:**
```
Status: inactive
```

> 📝 **Meaning**: Firewall is not active (default after installation).

---

## 1.2 View Default Rules

> 🔍 **Step 2**: View default firewall rules

```bash
sudo ufw show raw
```

### 📋 **Default rules:**
- **deny incoming** — reject all incoming connections
- **allow outgoing** — allow all outgoing connections

---

## 1.3 Allow SSH

> 🔑 **Step 3**: Open SSH port to avoid being locked out after enabling firewall

```bash
sudo ufw allow ssh
# or
sudo ufw allow 22/tcp
```

### 🔍 **Verification:**
```bash
sudo ufw show added
```

> ⚠️ **Important**: Allow SSH **BEFORE** enabling firewall, to avoid being locked out of remote access!

---

## 1.4 Enable Firewall

> 🛡️ **Step 4**: Enable the firewall

```bash
sudo ufw enable
```

A warning will appear, type `y` then Enter.

### 🔍 **Check status:**
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

## 1.5 Allow Other Ports

> 🌐 **Step 5**: Open additional ports for other services

```bash
# Allow HTTP (port 80)
sudo ufw allow 80/tcp

# Allow HTTPS (port 443)
sudo ufw allow 443/tcp
```

---

## 1.6 View Rules with Numbers

> 📊 **Step 6**: View rule list with numbered order

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

## 1.7 Delete Rules

> ❌ **Step 7**: Delete a rule by its number

```bash
# Delete rule number 3
sudo ufw delete 3
```

> 📝 **Note**: Rule numbers may change after deletion. Always check with `sudo ufw status numbered` before deleting.

---

## 1.8 Disable Firewall

> ⏸️ **Step 8**: Disable the firewall (if needed)

```bash
sudo ufw disable
```

> ⚠️ **Warning**: Disabling the firewall will open all ports. Only do this for troubleshooting!

---

# Chapter II: SSH Hardening

> 📖 *This chapter explains how to secure SSH configuration to prevent unauthorized access.*

## 2.1 Backup SSH Configuration

> 💾 **Step 1**: Create a backup of SSH configuration file before changes

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

> 📝 **Note**: Always backup before modifying important configurations!

---

## 2.2 Change SSH Port

> 🔧 **Step 2**: Change default SSH port from 22 to 2222

```bash
sudo nano /etc/ssh/sshd_config
```

Find `#Port 22`, change to:

```
Port 2222
```

Save: `Ctrl+X`, `Y`, `Enter`.

> 📝 **Reason**: Changing the default port reduces automated scanner attacks targeting port 22.

---

## 2.3 Allow New Port in Firewall

> 🛡️ **Step 3**: Open the new port in firewall and close the old one

```bash
# Allow new port
sudo ufw allow 2222/tcp

# Remove old port rule
sudo ufw delete allow 22/tcp

# Reload firewall
sudo ufw reload
```

> ⚠️ **Important**: Open the new port **BEFORE** closing the old one, to avoid being locked out!

---

## 2.4 Disable Root Login via SSH

> 🔒 **Step 4**: Prevent root login through SSH

In file `/etc/ssh/sshd_config`, add or change:

```
PermitRootLogin no
```

> 📝 **Reason**: Root login via SSH is very dangerous. Use `sudo` from a regular user instead.

---

## 2.5 Limit Users Allowed to Login via SSH

> 👤 **Step 5**: Restrict which users are allowed to login via SSH

Add a line in `/etc/ssh/sshd_config`:

```
AllowUsers gix andi
```

> 📝 **Meaning**: Only users `gix` and `andi` are allowed to login via SSH. Other users will be rejected.

---

## 2.6 Restart SSH and Verify

> 🔄 **Step 6**: Restart SSH service and verify configuration

```bash
# Restart SSH
sudo systemctl restart ssh

# Check SSH status
sudo systemctl status ssh

# Verify listening port
sudo ss -tulpn | grep 2222
```

### ✅ **Expected output:**
```
tcp   LISTEN 0    128    0.0.0.0:2222   0.0.0.0:*
tcp   LISTEN 0    128    [::]:2222      [::]:*
```

> 📝 **Meaning**: SSH is now running on port 2222 and ready to accept connections.

---

# Chapter III: SSH Key Authentication (Passwordless Login)

> 📖 *This chapter explains how to set up SSH authentication using cryptographic key pairs for passwordless login.*

## 3.1 Generate Key Pair on Laptop

> 🔑 **Step 1**: Generate a key pair (public & private) on your laptop

> ⚠️ **Do NOT do this on the VM!** Open PowerShell/CMD on your laptop:

```powershell
ssh-keygen -t ed25519 -C "gix@ubuntu-server"
```

- Press **Enter** for default location (`~/.ssh/id_ed25519`)
- Enter **empty passphrase** (Enter twice)

### ✅ **Output:**
```
Your identification has been saved in ~/.ssh/id_ed25519
Your public key has been saved in ~/.ssh/id_ed25519.pub
```

### 📋 **Explanation:**

| File | Description |
|---|---|
| `id_ed25519` | 🔒 Private key — **NEVER** share this! |
| `id_ed25519.pub` | 🔓 Public key — copy to server |

---

## 3.2 Copy Public Key to Server

> 📤 **Step 2**: Copy the public key to the server so it recognizes your laptop

### Manual method (since `ssh-copy-id` is not available on Windows):

```powershell
# Display public key
type ~/.ssh/id_ed25519.pub
```

Select and copy the entire line (from `ssh-ed25519` to the end).

In the SSH session on port 2222 (VM):

```bash
mkdir -p ~/.ssh
echo "ssh-ed25519 AAAAC3... gix@ubuntu-server" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### Alternative one-liner in PowerShell:

```powershell
type ~/.ssh/id_ed25519.pub | ssh -p 2222 gix@192.168.56.10 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

---

## 3.3 Set Private Key Permissions (Windows)

> 🔐 **Step 3**: Fix bad permissions error on private key

If you get a bad permissions error, create a new key in a separate folder (more secure):

```powershell
# Create dedicated folder
mkdir C:\Users\&lt;Username&gt;\.ssh-praktik
cd C:\Users\&lt;Username&gt;\.ssh-praktik

# Generate new key
ssh-keygen -t ed25519 -f ./id_ed25519_praktik -C "praktik@ubuntu-server"

# Copy public key to server
type .\id_ed25519_praktik.pub | ssh -p 2222 gix@192.168.56.10 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

> 📝 **Note**: A separate folder avoids permission conflicts with existing keys.

---

## 3.4 Test Passwordless Login

> 🧪 **Step 4**: Test SSH login using key

From laptop terminal (not from active SSH session):

```powershell
ssh -p 2222 -i C:\Users\&lt;Username&gt;\.ssh-praktik\id_ed25519_praktik gix@192.168.56.10
```

> ✅ If you can login without entering a password → **success!**

---

## 3.5 Disable Password Authentication

> 🔒 **Step 5**: Disable password login so only keys are accepted

After SSH key is working, in the SSH session on port 2222 (VM):

```bash
sudo nano /etc/ssh/sshd_config
```

Find `PasswordAuthentication yes`, change to:

```
PasswordAuthentication no
```

Restart SSH:

```bash
sudo systemctl restart ssh
```

### 🧪 **Test:**
- Login with SSH key → ✅ **Success**
- Login with password → ❌ **Rejected**

> ⚠️ **Warning**: Make sure SSH key works before disabling password auth, to avoid being locked out!

---

# Chapter IV: Basic Security Monitoring

> 📖 *This chapter explains how to monitor system security activity through logs and monitoring tools.*

## 4.1 View Authentication Log

> 📋 **Step 1**: View authentication attempt logs

```bash
sudo tail -20 /var/log/auth.log
```

> 📝 **Meaning**: You will see login attempts (successful/failed) with timestamps and user information.

---

## 4.2 Find Failed Login Attempts

> 🔍 **Step 2**: Detect failed login attempts (indication of attacks)

```bash
# Find failed logins
sudo grep "Failed password" /var/log/auth.log | tail -10
```

View rejections due to `AllowUsers`:

```bash
sudo grep "not allowed" /var/log/auth.log | tail -5
```

> 📝 **Note**: Many failed login attempts from the same IP could indicate a brute-force attack.

---

## 4.3 View User Login History

> 📊 **Step 3**: View who has logged into the system

```bash
last -10
```

> 📝 **Meaning**: Displays the last 10 logins with user, terminal, time, and origin IP information.

---

## 4.4 Active Monitoring with journalctl

> 📡 **Step 4**: Monitor SSH logs in real-time

```bash
sudo journalctl -u ssh -f
```

Press `Ctrl+C` to stop.

> 📝 **Meaning**: The `-f` (follow) flag displays logs in real-time, useful for monitoring SSH activity as it happens.

---

# Chapter V: Important Commands Summary

> 📚 *Summary of important commands learned in this session.*

## 🛡️ UFW (Firewall)

| Command | Function |
|---|---|
| `sudo ufw status` | Check firewall status |
| `sudo ufw enable` | Enable firewall |
| `sudo ufw disable` | Disable firewall |
| `sudo ufw allow port/tcp` | Allow port |
| `sudo ufw delete number` | Delete rule |
| `sudo ufw status numbered` | View rules with numbers |
| `sudo ufw show added` | View added rules |
| `sudo ufw reload` | Reload firewall rules |

## 🔧 SSH Hardening

| Command | Function |
|---|---|
| `sudo nano /etc/ssh/sshd_config` | Edit SSH configuration |
| `sudo systemctl restart ssh` | Restart SSH |
| `sudo systemctl status ssh` | Check SSH status |
| `sudo ss -tulpn \| grep ssh` | Check SSH listening port |

## 🔑 SSH Key

| Command (on laptop) | Function |
|---|---|
| `ssh-keygen -t ed25519` | Generate key pair |
| `type ~/.ssh/id_ed25519.pub` | Display public key |
| `ssh -p port -i key user@ip` | Login with key |

## 📊 Monitoring

| Command | Function |
|---|---|
| `sudo tail -20 /var/log/auth.log` | View authentication log |
| `sudo grep "Failed password" /var/log/auth.log` | Find failed logins |
| `sudo grep "not allowed" /var/log/auth.log` | Find rejected users |
| `last -10` | View login history |
| `sudo journalctl -u ssh -f` | Monitor SSH logs real-time |

---

# Chapter VI: Success Checklist

> ✅ **Verification checklist** to ensure all configurations work properly

## 🛡️ Firewall (UFW)

| No | Item | Verification | Status |
|---|---|---|---|
| 1 | 🛡️ UFW active | `sudo ufw status verbose` | ✅ |
| 2 | 🔓 Port 2222 allowed | `sudo ufw status numbered` | ✅ |
| 3 | 🔒 Port 22 denied (after migration) | `sudo ufw status numbered` | ✅ |

## 🔧 SSH Hardening

| No | Item | Verification | Status |
|---|---|---|---|
| 1 | 🔧 SSH running on port 2222 | `sudo ss -tulpn \| grep 2222` | ✅ |
| 2 | 🔒 Root login via SSH disabled | `grep PermitRootLogin /etc/ssh/sshd_config` | ✅ |
| 3 | 👤 Only users gix and andi allowed | `grep AllowUsers /etc/ssh/sshd_config` | ✅ |

## 🔑 SSH Key Authentication

| No | Item | Verification | Status |
|---|---|---|---|
| 1 | 🔑 Key pair successfully generated on laptop | `ls ~/.ssh/id_ed25519*` | ✅ |
| 2 | 📤 Public key copied to server | `cat ~/.ssh/authorized_keys` | ✅ |
| 3 | ✅ Passwordless login successful | `ssh -p 2222 -i key gix@ip` | ✅ |
| 4 | 🔒 Password authentication disabled | `grep PasswordAuthentication /etc/ssh/sshd_config` | ✅ |

## 📊 Monitoring

| No | Item | Verification | Status |
|---|---|---|---|
| 1 | 📋 Can view authentication log | `sudo tail /var/log/auth.log` | ✅ |
| 2 | 🔍 Can find failed login attempts | `sudo grep "Failed password" /var/log/auth.log` | ✅ |
| 3 | 📊 Can view login history | `last -10` | ✅ |
| 4 | 📡 Can monitor SSH logs real-time | `sudo journalctl -u ssh -f` | ✅ |

---

# Chapter VII: Conclusion

> 🎉 This concludes the System Administration Lab Session 5: System Security documentation.

Upon completing this documentation, the practitioner has mastered:

- 🛡️ **UFW Firewall** — enabling, opening/closing ports, viewing rules
- 🔧 **SSH Hardening** — changing port, disabling root login, limiting users
- 🔑 **SSH Key Authentication** — passwordless login with cryptographic keys
- 📊 **Security Monitoring** — reading authentication logs, detecting attacks

---

# Chapter VIII: References

> 📚 **References and documentation** used during the practicum

## 🛡️ UFW Documentation
- **Website**: [https://help.ubuntu.com/community/UFW](https://help.ubuntu.com/community/UFW)
- **Topics**: UFW basics, allowing/denying ports, status commands

## 🔧 SSH Hardening
- **Website**: [https://www.ssh.com/academy/ssh/sshd_config](https://www.ssh.com/academy/ssh/sshd_config)
- **Topics**: sshd_config options, PermitRootLogin, AllowUsers

## 🔑 SSH Key Authentication
- **Website**: [https://www.ssh.com/academy/ssh/keygen](https://www.ssh.com/academy/ssh/keygen)
- **Topics**: ssh-keygen, ed25519, authorized_keys

## 📊 Monitoring & Logging
- **Website**: [https://manpages.ubuntu.com/manpages/](https://manpages.ubuntu.com/manpages/)
- **Topics**: journalctl, auth.log, last, grep

### 🛡️ **Best Practices**
- Always allow SSH before enabling firewall
- Always backup configuration before changes
- Use a non-standard port for SSH
- Disable root login via SSH
- Limit users who can login via SSH
- Use SSH keys instead of passwords
- Monitor logs regularly for attack detection

---

> 🎯 **Conclusion**: This documentation covers the entire System Security practicum for System Administration Session 5.

> 📅 **Last updated**: `[19/04/2026]`
> 👨‍💻 **By**: `[Afrizal Ilza Munadhif]` - `[231240001420]`
> 🔗 **Repository**: [https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab](https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab)
