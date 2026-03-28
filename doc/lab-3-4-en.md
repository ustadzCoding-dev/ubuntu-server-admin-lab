# System Administration Lab Documentation

> **Sessions 3–4: User, Group, and File Permission Management**
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

- [Chapter I: User Management](#chapter-i-user-management)
  - [1.1 Understanding User Configuration Files](#11-understanding-user-configuration-files)
  - [1.2 Creating User with adduser](#12-creating-user-with-adduser)
  - [1.3 Creating User with useradd](#13-creating-user-with-useradd)
  - [1.4 Creating User with Specific Primary Group](#14-creating-user-with-specific-primary-group)
  - [1.5 Viewing User Information](#15-viewing-user-information)
  - [1.6 Login as Another User](#16-login-as-another-user)
  - [1.7 Modifying User (usermod)](#17-modifying-user-usermod)
  - [1.8 Deleting User (userdel)](#18-deleting-user-userdel)
- [Chapter II: Group Management](#chapter-ii-group-management)
  - [2.1 Creating Group](#21-creating-group)
  - [2.2 Adding User to Supplementary Group](#22-adding-user-to-supplementary-group)
  - [2.3 Viewing Group Information](#23-viewing-group-information)
  - [2.4 Primary Group vs Supplementary Group](#24-primary-group-vs-supplementary-group)
  - [2.5 Modifying and Deleting Group](#25-modifying-and-deleting-group)
- [Chapter III: File Permissions](#chapter-iii-file-permissions)
  - [3.1 Understanding rwx Permissions](#31-understanding-rwx-permissions)
  - [3.2 Changing Permissions with Numeric (chmod)](#32-changing-permissions-with-numeric-chmod)
  - [3.3 Changing Permissions with Symbolic](#33-changing-permissions-with-symbolic)
  - [3.4 Changing Ownership (chown, chgrp)](#34-changing-ownership-chown-chgrp)
- [Chapter IV: Folder Structure and Permissions](#chapter-iv-folder-structure-and-permissions)
  - [4.1 Creating Folder Structure](#41-creating-folder-structure)
  - [4.2 Public Folder (755)](#42-public-folder-755)
  - [4.3 Team Folder with Group (770)](#43-team-folder-with-group-770)
  - [4.4 Private Folder (700)](#44-private-folder-700)
  - [4.5 Sticky Bit (1777)](#45-sticky-bit-1777)
- [Chapter V: User and Group Deletion Practice](#chapter-v-user-and-group-deletion-practice)
  - [5.1 Deleting User (userdel)](#51-deleting-user-userdel)
  - [5.2 Deleting Group (groupdel)](#52-deleting-group-groupdel)
  - [5.3 Checking Deleted Users and Groups](#53-checking-deleted-users-and-groups)
- [Chapter VI: Important Commands Summary](#chapter-vi-important-commands-summary)
- [Chapter VII: Success Checklist](#chapter-vii-success-checklist)
- [Chapter VIII: Practitioner Notes](#chapter-viii-practitioner-notes)
- [Chapter IX: References](#chapter-ix-references)

---

# Chapter I: User Management

> 📖 *This chapter explains how to create, modify, and manage users in Ubuntu Server.*

## 1.1 Understanding User Configuration Files

> 🔍 **Step 1**: View all users with home directory

```bash
# View all users with home directory
cat /etc/passwd | grep /home
```

### ✅ **Initial output:**
```
gix:x:1000:1000:gix:/home/gix:/usr/bin/zsh
```

### 📋 **Format /etc/passwd:**
```
username:password:UID:GID:comment:home:shell
```

| Field | Example | Description |
|---|---|---|
| username | gix | Login name |
| password | x | Password stored in /etc/shadow |
| UID | 1000 | User ID (0=root, 1000=first user) |
| GID | 1000 | Group ID (primary group) |
| comment | gix | User full name |
| home | /home/gix | Home directory |
| shell | /usr/bin/zsh | Default shell |

> 🔍 **Step 2**: View encrypted password

```bash
# View encrypted password (root only)
sudo cat /etc/shadow | grep gix

# Format: username:encrypted_password:last_change:min:max:warn:inactive:expire
```

---

## 1.2 Creating User with adduser

> 📝 **Creating new user** with interactive method

```bash
# Create user andi with adduser (interactive)
sudo adduser andi
```

### ✅ **Fill in data:**
```
Password: andi123
Full Name []: Andi Setiawan
Room Number []: (empty)
Work Phone []: (empty)
Home Phone []: (empty)
Other []: (empty)
Is the information correct? [Y/n]: Y
```

### 🔍 **Verification:**
```bash
cat /etc/passwd | grep andi
# Output: andi:x:1001:1001:Andi Setiawan,,,:/home/andi:/bin/bash
```

---

## 1.3 Creating User with useradd

> ⚙️ **Creating new user** with non-interactive method

```bash
# Create user siti with useradd (non-interactive)
sudo useradd -m -s /bin/bash siti

# Set password for siti
sudo passwd siti
# New password: siti123
# Retype new password: siti123
```

### 📋 **useradd options explanation:**

| Option | Description |
|---|---|
| `-m` | Create home directory |
| `-s /bin/bash` | Set default shell |
| `-g group` | Set primary group |
| `-G group1,group2` | Add to supplementary groups |
| `-c "comment"` | Add comment/full name |

---

## 1.4 Creating User with Specific Primary Group

> 🎯 **Creating user** with predefined primary group

```bash
# Create webdev group first
sudo groupadd webdev

# Create user budi with primary group webdev
sudo useradd -m -s /bin/bash -g webdev budi

# Set password
sudo passwd budi
# Password: budi123
```

### 🔍 **Verification:**
```bash
id budi
# Output: uid=1003(budi) gid=1003(webdev) groups=1003(webdev)
```

---

## 1.5 Viewing User Information

> 📊 **Viewing complete information** about users

```bash
# View complete user information
id andi
# Output: uid=1001(andi) gid=1001(andi) groups=1001(andi)

id siti
# Output: uid=1002(siti) gid=1002(siti) groups=1002(siti)

id budi
# Output: uid=1003(budi) gid=1003(webdev) groups=1003(webdev)

# View currently logged in users
who
# Output: gix     pts/0        2026-03-28 10:00 (192.168.56.1)

# View login history
last -5

# View users by shell
cat /etc/passwd | grep /bin/bash
```

---

## 1.6 Login as Another User

> 🔐 **Switching to another user** for testing or administration

```bash
# Login as andi
sudo su - andi
pwd               # /home/andi
whoami            # andi
exit              # Return to gix

# Login as siti
sudo su - siti
pwd               # /home/siti
exit

# Login as budi
sudo su - budi
pwd               # /home/budi
id                # uid=1003(budi) gid=1003(webdev)
exit
```

---

## 1.7 Modifying User (usermod)

> ✏️ **Modifying existing user** properties

```bash
# Change user shell
sudo usermod -s /bin/zsh andi
# Check: grep andi /etc/passwd → /home/andi:/bin/zsh

# Change username (login name)
sudo usermod -l andi_baru andi
# Check: grep andi /etc/passwd → andi_baru:x:1001:...

# Revert name (if needed)
sudo usermod -l andi andi_baru

# Change primary group
sudo usermod -g webdev siti
id siti
# Output: uid=1002(siti) gid=1003(webdev) groups=1003(webdev)

# Revert primary group
sudo usermod -g siti siti

# Change home directory
sudo usermod -d /home/andi_new andi
```

---

## 1.8 Deleting User (userdel)

> ❌ **Removing user** from the system

### 📋 **Step 1: Create temporary user for deletion practice**
```bash
# Create temporary user for deletion practice
sudo useradd -m -s /bin/bash tempuser
sudo passwd tempuser
# Password: temp123

# Check user exists
cat /etc/passwd | grep tempuser
# Output: tempuser:x:1004:1004::/home/tempuser:/bin/bash
```

### 📋 **Step 2: Delete user WITHOUT removing home directory**
```bash
# Delete user WITHOUT removing home directory
sudo userdel tempuser

# Check: user no longer in /etc/passwd
cat /etc/passwd | grep tempuser
# Output: (empty)

# Home directory still exists
ls -la /home/ | grep tempuser
# Output: drwxr-x--- 3 1004 1004 4096 ... tempuser
```

### 📋 **Step 3: Delete user WITH home directory**
```bash
# Create new user for deletion practice with home
sudo useradd -m -s /bin/bash tempuser2
sudo passwd tempuser2
# Password: temp123

# Delete user WITH home directory
sudo userdel -r tempuser2

# Check: user and home are gone
cat /etc/passwd | grep tempuser2
ls -la /home/ | grep tempuser2
# Output: (empty)
```

### 📊 **userdel differences:**

| Command | Home Directory | Other Files | When to Use |
|---|---|---|---|
| `sudo userdel name` | Remains | Remains | User left, data needs archiving |
| `sudo userdel -r name` | Deleted | Deleted | User finished, no data needed |

```bash
# Delete orphaned files from deleted users
# Find files with non-existent UID
find /home -nouser -ls
```

---

# Chapter II: Group Management

> 👥 *This chapter explains how to create, modify, and manage groups in Ubuntu Server.*

## 2.1 Creating Group

> ➕ **Creating new group** for user organization

```bash
# Create new groups
sudo groupadd webdev
sudo groupadd project_alpha
sudo groupadd marketing

# Create group with specific GID
sudo groupadd -g 2000 sales

# View created groups
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

## 2.2 Adding User to Supplementary Group

> 🔗 **Adding user** to supplementary group

```bash
# Add andi to webdev group
sudo usermod -aG webdev andi

# Add siti to webdev and project_alpha groups
sudo usermod -aG webdev,project_alpha siti

# Add andi to marketing group
sudo usermod -aG marketing andi
```

### 📋 **Explanation:**
- `-a` = append (add, don't remove other groups)
- `-G` = specify supplementary group

### 🔍 **Verification:**
```bash
id andi
# Output: uid=1001(andi) gid=1001(andi) groups=1001(andi),1003(webdev),1005(marketing)

id siti
# Output: uid=1002(siti) gid=1002(siti) groups=1002(siti),1003(webdev),1004(project_alpha)
```

---

## 2.3 Viewing Group Information

> 📊 **Viewing information** about groups and members

```bash
# View all groups
cat /etc/group

# View specific group and its members
cat /etc/group | grep webdev
# Output: webdev:x:1003:andi,siti
# Note: budi doesn't appear because he's primary group, not supplementary

# View groups owned by user
groups andi
# Output: andi webdev marketing

groups siti
# Output: siti webdev project_alpha
```

---

## 2.4 Primary Group vs Supplementary Group

> 🔄 **Difference** between primary group and supplementary group

| Type | How to Check | Display in /etc/group | Example |
|---|---|---|---|
| Primary Group | `id budi` → gid=1003(webdev) | Members empty | webdev:x:1003: |
| Supplementary Group | `id andi` → groups=1003(webdev) | Has user names | webdev:x:1003:andi,siti |

### 📋 **Example:**
```bash
# budi has webdev as primary group
id budi
# uid=1003(budi) gid=1003(webdev) groups=1003(webdev)

# andi has webdev as supplementary group
id andi
# uid=1001(andi) gid=1001(andi) groups=1001(andi),1003(webdev)
```

---

## 2.5 Modifying and Deleting Group

> ✏️ **Modifying and deleting** existing groups

```bash
# Rename group
sudo groupmod -n dev webdev
# webdev becomes dev

# Change group GID
sudo groupmod -g 3000 dev
# dev GID becomes 3000

# Revert name (for practice consistency)
sudo groupmod -n webdev dev
sudo groupmod -g 1003 webdev

# Create temporary group for deletion practice
sudo groupadd tempgroup

# Delete group (ensure no members)
sudo groupdel tempgroup
```

> ⚠️ **Warning**: Group cannot be deleted if there are users with it as primary group.

```bash
# Example: webdev group cannot be deleted because budi has it as primary group
sudo groupdel webdev
# Output: groupdel: cannot remove the primary group of user 'budi'
```

---

# Chapter III: File Permissions

> 🔐 *This chapter explains file permission concepts and how to change them.*

## 3.1 Understanding rwx Permissions

> 🔍 **Viewing file** and directory permissions

```bash
# View file permissions
ls -l
```

### ✅ **Example output:**
```
-rwxr-xr-- 1 gix developer 1024 Mar 28 10:00 script.sh
```

### 📋 **Permission explanation:**

| Position | Meaning |
|---|---|
| `-` | File (or `d` for directory) |
| `rwx` | Owner (gix): read, write, execute |
| `r-x` | Group (developer): read, execute |
| `r--` | Others: read |

### 📊 **Numeric Permission:**

| Number | Binary | Permission |
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

## 3.2 Changing Permissions with Numeric (chmod)

> 🔢 **Changing permissions** using numbers

```bash
# Create test file
touch ~/test_permissions.txt
ls -l ~/test_permissions.txt
# Output: -rw-rw-r-- 1 gix gix 0 Mar 28 16:03 test_permissions.txt

# Change to 700 (rwx------)
chmod 700 ~/test_permissions.txt
ls -l ~/test_permissions.txt
# Output: -rwx------ 1 gix gix 0 Mar 28 16:03 test_permissions.txt

# Change to 750 (rwxr-x---)
chmod 750 ~/test_permissions.txt
ls -l ~/test_permissions.txt
# Output: -rwxr-x--- 1 gix gix 0 Mar 28 16:03 test_permissions.txt

# Change to 755 (rwxr-xr-x)
chmod 755 ~/test_permissions.txt
ls -l ~/test_permissions.txt
# Output: -rwxr-xr-x 1 gix gix 0 Mar 28 16:03 test_permissions.txt

# Change to 644 (rw-r--r--)
chmod 644 ~/test_permissions.txt
ls -l ~/test_permissions.txt
# Output: -rw-r--r-- 1 gix gix 0 Mar 28 16:03 test_permissions.txt
```

---

## 3.3 Changing Permissions with Symbolic

> 🔤 **Changing permissions** using symbols

```bash
# Recreate test file
echo "test" > ~/test_sym.txt
ls -l ~/test_sym.txt
# Output: -rw-rw-r-- 1 gix gix 5 Mar 28 16:10 test_sym.txt

# Add execute for owner (u+x)
chmod u+x ~/test_sym.txt
ls -l ~/test_sym.txt
# Output: -rwxrw-r-- 1 gix gix 5 Mar 28 16:10 test_sym.txt

# Remove write for group (g-w)
chmod g-w ~/test_sym.txt
ls -l ~/test_sym.txt
# Output: -rwxr--r-- 1 gix gix 5 Mar 28 16:10 test_sym.txt

# Set permission for group and others (g=rx,o=rx)
chmod g=rx,o=rx ~/test_sym.txt
ls -l ~/test_sym.txt
# Output: -rwxr-xr-x 1 gix gix 5 Mar 28 16:10 test_sym.txt

# Set owner=rwx, group=rx, other=none (equivalent to 750)
chmod u=rwx,g=rx,o= ~/test_sym.txt
ls -l ~/test_sym.txt
# Output: -rwxr-x--- 1 gix gix 5 Mar 28 16:10 test_sym.txt
```

### 📋 **Symbolic Syntax:**

| Target | Code | Operator | Permission |
|---|---|---|---|
| user/owner | u | `+` (add) | r (read) |
| group | g | `-` (remove) | w (write) |
| others | o | `=` (set) | x (execute) |
| all | a | | |

---

## 3.4 Changing Ownership (chown, chgrp)

> 👤 **Changing ownership** of files and directories

```bash
# Create test file
touch ~/test_ownership.txt
ls -l ~/test_ownership.txt
# Output: -rw-rw-r-- 1 gix gix 0 Mar 28 16:15 test_ownership.txt

# Change file owner
sudo chown andi ~/test_ownership.txt
ls -l ~/test_ownership.txt
# Output: -rw-rw-r-- 1 andi gix 0 Mar 28 16:15 test_ownership.txt

# Change owner and group
sudo chown gix:webdev ~/test_ownership.txt
ls -l ~/test_ownership.txt
# Output: -rw-rw-r-- 1 gix webdev 0 Mar 28 16:15 test_ownership.txt

# Change group only
sudo chgrp project_alpha ~/test_ownership.txt
ls -l ~/test_ownership.txt
# Output: -rw-rw-r-- 1 gix project_alpha 0 Mar 28 16:15 test_ownership.txt

# Recursive (for directories)
sudo chown -R andi:webdev /data/test
```

---

# Chapter IV: Folder Structure and Permissions

> 📁 *This chapter explains how to create folder structures with appropriate permissions.*

## 4.1 Creating Folder Structure

> 🏗️ **Creating folder structure** for data organization

```bash
# Create folder structure
sudo mkdir -p /data/{public,team,private,backup}

# View structure
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

## 4.2 Public Folder (755)

> 🌐 **Configuring public folder** accessible by all users

```bash
# Set permission for public folder
sudo chmod 755 /data/public
sudo chown root:root /data/public

# Check
ls -ld /data/public
# Output: drwxr-xr-x 2 root root 4096 Mar 28 15:47 /data/public
```

> 📝 **Meaning**: All users can read/view folder contents, only root can write.

### 🧪 **Test as regular user:**
```bash
sudo su - andi
ls /data/public          # ✅ Can
touch /data/public/test.txt  # ❌ Permission denied
exit
```

---

## 4.3 Team Folder with Group (770)

> 👥 **Configuring team folder** accessible only by group members

```bash
# Create special group
sudo groupadd project_alpha

# Add andi and siti to group
sudo usermod -aG project_alpha andi
sudo usermod -aG project_alpha siti

# Set team folder
sudo chown :project_alpha /data/team
sudo chmod 770 /data/team

# Check
ls -ld /data/team
# Output: drwxrwx--- 2 root project_alpha 4096 Mar 28 15:47 /data/team
```

> 📝 **Meaning**: Only owner (root) and project_alpha group members can access.

### 🧪 **Test:**
```bash
sudo su - andi
cd /data/team && touch andi.txt && ls    # ✅ Can
exit

sudo su - budi
cd /data/team    # ❌ Permission denied
exit
```

---

## 4.4 Private Folder (700)

> 🔒 **Configuring private folder** accessible only by owner

```bash
# Create private folder for each user
sudo mkdir /data/private/andi
sudo mkdir /data/private/siti

# Set ownership
sudo chown andi:andi /data/private/andi
sudo chown siti:siti /data/private/siti

# Set permission
sudo chmod 700 /data/private/andi
sudo chmod 700 /data/private/siti

# Set parent folder permission
sudo chmod 750 /data/private

# Check
ls -ld /data/private/*
```

### ✅ **Output:**
```
drwx------ 2 andi andi 4096 Mar 28 15:54 /data/private/andi
drwx------ 2 siti siti 4096 Mar 28 15:54 /data/private/siti
```

### 🧪 **Test:**
```bash
sudo su - andi
cd /data/private/andi && touch andi.txt && ls    # ✅ Can
cd /data/private/siti                            # ❌ Permission denied
exit
```

---

## 4.5 Sticky Bit (1777)

> 🛡️ **Configuring sticky bit** for shared folders

> 📝 **Sticky Bit** is used on shared folders so users can only delete their own files.

```bash
# Set sticky bit
sudo chmod 1777 /data/backup

# Check
ls -ld /data/backup
# Output: drwxrwxrwt 2 root root 4096 Mar 28 15:47 /data/backup
```

> ⚠️ **Note** the letter `t` at the end of permission!

### 🧪 **Test Sticky Bit:**
```bash
# Login as andi
sudo su - andi

# Create file (use redirect >)
echo "File from andi" > /data/backup/andi.txt
ls -l /data/backup/
exit

# Login as siti
sudo su - siti

# Create own file
echo "File from siti" > /data/backup/siti.txt
ls -l /data/backup/

# Try to delete andi's file
rm /data/backup/andi.txt
# Output: rm: cannot remove '/data/backup/andi.txt': Operation not permitted

# Delete own file (success)
rm /data/backup/siti.txt
exit
```

---

# Chapter V: User and Group Deletion Practice

> 🛠️ *This chapter practices how to properly delete users and groups.*

## 5.1 Deleting User (userdel)

> ❌ **Removing user** from the system

### 📋 **Step 1: Create temporary user for deletion practice**
```bash
# Create tempuser
sudo useradd -m -s /bin/bash tempuser
sudo passwd tempuser
# Password: temp123

# Create tempuser2
sudo useradd -m -s /bin/bash tempuser2
sudo passwd tempuser2
# Password: temp123
```

### 📋 **Step 2: Verify users created**
```bash
# Check in /etc/passwd
cat /etc/passwd | grep tempuser
# Output: tempuser:x:1004:1004::/home/tempuser:/bin/bash

cat /etc/passwd | grep tempuser2
# Output: tempuser2:x:1005:1005::/home/tempuser2:/bin/bash

# Check home directory
ls -la /home/ | grep tempuser
# Output: drwxr-x--- 3 tempuser tempuser 4096 ... tempuser
ls -la /home/ | grep tempuser2
# Output: drwxr-x--- 3 tempuser2 tempuser2 4096 ... tempuser2
```

### 📋 **Step 3: Delete user WITHOUT removing home directory**
```bash
# Delete tempuser (without -r)
sudo userdel tempuser

# Check user no longer exists
cat /etc/passwd | grep tempuser
# Output: (empty)

# Home directory STILL exists
ls -la /home/ | grep tempuser
# Output: drwxr-x--- 3 1004 1004 4096 ... tempuser
```

### 📋 **Step 4: Delete user WITH home directory**
```bash
# Delete tempuser2 (with -r)
sudo userdel -r tempuser2

# Check user no longer exists
cat /etc/passwd | grep tempuser2
# Output: (empty)

# Home directory ALSO gone
ls -la /home/ | grep tempuser2
# Output: (empty)
```

### 📋 **Step 5: Clean up orphaned home directory**
```bash
# tempuser home directory still exists with owner UID 1004 (no user exists)
# Delete manually if needed
sudo rm -rf /home/tempuser
```

### 📊 **userdel comparison:**

| Command | Home Directory | Other Files | When to Use |
|---|---|---|---|
| `sudo userdel name` | Remains | Remains | User left, data needs archiving |
| `sudo userdel -r name` | Deleted | Deleted | User finished, no data needed |

---

## 5.2 Deleting Group (groupdel)

> ❌ **Removing group** from the system

### 📋 **Step 1: Create temporary group**
```bash
# Create groups for practice
sudo groupadd tempgroup
sudo groupadd tempgroup2

# Check groups
cat /etc/group | grep tempgroup
# Output: tempgroup:x:1006:
cat /etc/group | grep tempgroup2
# Output: tempgroup2:x:1007:
```

### 📋 **Step 2: Delete empty group**
```bash
# Delete tempgroup2
sudo groupdel tempgroup2

# Check it's gone
cat /etc/group | grep tempgroup2
# Output: (empty)
```

### 📋 **Step 3: Try deleting group with members**
```bash
# Create user with tempgroup as primary group
sudo useradd -m -g tempgroup tempuser3
sudo passwd tempuser3
# Password: temp123

# Try to delete tempgroup
sudo groupdel tempgroup
```

### ✅ **Expected output:**
```
groupdel: cannot remove the primary group of user 'tempuser3'
```

> 📝 **Explanation**: Group cannot be deleted if there are users with it as primary group.

### 📋 **Step 4: Delete user first**
```bash
# Delete user still using the group
sudo userdel -r tempuser3

# Now tempgroup can be deleted
sudo groupdel tempgroup

# Verify
cat /etc/group | grep tempgroup
# Output: (empty)
```

---

## 5.3 Checking Deleted Users and Groups

> 🔍 **Checking orphaned files** from deleted users/groups

```bash
# Find files with non-existent UID (orphaned files)
find /home -nouser -ls 2>/dev/null

# Find files with non-existent GID
find /home -nogroup -ls 2>/dev/null

# Clean up if found
sudo find /home -nouser -delete 2>/dev/null
```

---

# Chapter VI: Important Commands Summary

> 📚 *Summary of important commands learned in this session.*

## 👤 User Management

| Command | Function |
|---|---|
| `sudo adduser name` | Create new user (interactive) |
| `sudo useradd -m -s /bin/bash name` | Create new user (non-interactive) |
| `sudo passwd name` | Change user password |
| `sudo usermod -aG group name` | Add user to supplementary group |
| `sudo usermod -l new_name old_name` | Change username |
| `sudo usermod -s /bin/zsh name` | Change user shell |
| `sudo userdel name` | Delete user (home remains) |
| `sudo userdel -r name` | Delete user with home directory |
| `id name` | View user information |
| `who` | View currently logged in users |
| `last` | View login history |
| `cat /etc/passwd` | View all users |

## 👥 Group Management

| Command | Function |
|---|---|
| `sudo groupadd name` | Create new group |
| `sudo groupmod -n new_name old_name` | Rename group |
| `sudo groupmod -g new_GID name` | Change group GID |
| `sudo groupdel name` | Delete group (must be empty) |
| `cat /etc/group` | View all groups |
| `groups name` | View groups owned by user |

## 🔐 File Permissions

| Command | Function |
|---|---|
| `chmod 755 file` | Change permission with numeric |
| `chmod u+x,g=rx file` | Change permission with symbolic |
| `sudo chown user:group file` | Change owner and group |
| `sudo chgrp group file` | Change group only |
| `ls -l file` | View permission |
| `ls -ld directory` | View directory permission |

## 📊 Commonly Used Numeric Permissions

| Permission | Numeric | Meaning | Usage |
|---|---|---|---|
| rwx------ | 700 | Owner only | User private folder |
| rwxr-x--- | 750 | Owner + group read/exec | Team folder |
| rwxrwx--- | 770 | Owner + group full access | Team collaboration |
| rwxr-xr-x | 755 | All can read/exec | Public folder |
| rw-r--r-- | 644 | Public file (default) | Configuration file |
| rwxrwxrwt | 1777 | Sticky bit | Shared folder (/tmp) |

---

# Chapter VII: Success Checklist

> ✅ **Verification checklist** to ensure all configurations work properly

## 👤 User & Group Management

| No | Item | Verification | Status |
|---|---|---|---|
| 1 | 👤 Users andi, siti, budi successfully created | `id andi` | ✅ |
| 2 | 🔐 Password for each user set | `sudo passwd name` | ✅ |
| 3 | 🆔 `id andi` shows appropriate groups | `id andi` | ✅ |
| 4 | 🆔 `id budi` shows webdev as primary group | `id budi` | ✅ |
| 5 | 👥 Groups webdev and project_alpha successfully created | `cat /etc/group` | ✅ |
| 6 | ➕ andi and siti added to webdev and project_alpha (supplementary) | `id andi` | ✅ |
| 7 | 🔑 budi has webdev as primary group | `id budi` | ✅ |
| 8 | ❌ User tempuser successfully deleted (without -r) | `cat /etc/passwd` | ✅ |
| 9 | ❌ User tempuser2 successfully deleted (with -r) | `cat /etc/passwd` | ✅ |
| 10 | ❌ Group tempgroup successfully deleted after user deleted | `cat /etc/group` | ✅ |

## 🔐 File Permissions

| No | Item | Verification | Status |
|---|---|---|---|
| 1 | 📁 Folder /data/public with permission 755 | `ls -ld /data/public` | ✅ |
| 2 | 📁 Folder /data/team with permission 770, group project_alpha | `ls -ld /data/team` | ✅ |
| 3 | 📁 Folders /data/private/andi and /data/private/siti with permission 700 | `ls -ld /data/private/*` | ✅ |
| 4 | 📁 Parent folder /data/private with permission 750 | `ls -ld /data/private` | ✅ |
| 5 | ✅ andi can access own folder, cannot access siti's folder | `sudo su - andi` | ✅ |
| 6 | ✅ andi and siti can access /data/team | `sudo su - andi` | ✅ |
| 7 | ❌ budi cannot access /data/team | `sudo su - budi` | ✅ |

## 🛡️ Sticky Bit

| No | Item | Verification | Status |
|---|---|---|---|
| 1 | 📁 Folder /data/backup has sticky bit (drwxrwxrwt) | `ls -ld /data/backup` | ✅ |
| 2 | ✅ andi can create file in /data/backup | `sudo su - andi` | ✅ |
| 3 | ✅ siti can create file in /data/backup | `sudo su - siti` | ✅ |
| 4 | ❌ siti cannot delete andi's file | `sudo su - siti` | ✅ |
| 5 | ✅ siti can delete own file | `sudo su - siti` | ✅ |

## 🗑️ User & Group Deletion

| No | Item | Verification | Status |
|---|---|---|---|
| 1 | ❌ User deleted with userdel (without -r) leaves home directory | `ls -la /home/` | ✅ |
| 2 | ❌ User deleted with userdel -r removes home directory | `ls -la /home/` | ✅ |
| 3 | ❌ Empty group can be deleted with groupdel | `cat /etc/group` | ✅ |
| 4 | ⚠️ Group with primary group members cannot be deleted | `sudo groupdel name` | ✅ |

---

# Chapter VIII: Practitioner Notes

> 📝 *Personal notes about difficulties encountered and important learnings during the practicum.*

## 😟 Difficulties Encountered

1. **Difference between adduser and useradd** - adduser is interactive, useradd requires manual options
2. **Primary vs Supplementary Group** - confused with display in /etc/group
3. **Numeric permission** - need binary to decimal conversion to understand
4. **Sticky bit** - letter `t` in permission only understood after practice
5. **User with specific primary group** - must create group first before creating user
6. **Group cannot be deleted** - if there are users with it as primary group
7. **Home directory left behind** - when using userdel without -r option

## 🎓 Important Learnings

1. **User management** is a fundamental skill for system administrators
2. **Groups** simplify permission management for many users
3. **rwx permissions** are crucial for file and directory security
4. **Numeric permission** is faster, symbolic is easier to understand
5. **Sticky bit** is important for shared folders so users don't delete each other's files
6. **Ownership** must be set with chown/chgrp for correct access
7. **User cleanup** must be done carefully with -r option to remove home directory
8. **Permission testing** must be done by logging in as the actual user

---

# Chapter IX: References

> 📚 **References and documentation** used during the practicum

## 🐧 Ubuntu Server Documentation
- **Website**: [https://ubuntu.com/server/docs](https://ubuntu.com/server/docs)
- **Topics**: User management, group management, file permissions

## 👤 User Management Commands
- **Website**: [https://manpages.ubuntu.com/manpages/](https://manpages.ubuntu.com/manpages/)
- **Topics**: useradd, usermod, userdel, adduser

## 👥 Group Management Commands
- **Website**: [https://manpages.ubuntu.com/manpages/](https://manpages.ubuntu.com/manpages/)
- **Topics**: groupadd, groupmod, groupdel

## 🔐 File Permissions
- **Website**: [https://wiki.archlinux.org/title/File_permissions_and_attributes](https://wiki.archlinux.org/title/File_permissions_and_attributes)
- **Topics**: chmod, chown, chgrp, sticky bit

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

> 🎯 **Conclusion**: This documentation covers the entire User, Group, and File Permission Management practicum for System Administration Sessions 3-4.

> 📅 **Last updated**: `[11/03/2026]`
> 👨‍💻 **By**: `[Afrizal Ilza Munadhif]` - `[231240001420]`
> 🔗 **Repository**: [https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab](https://github.com/ustadzCoding-dev/ubuntu-server-admin-lab)
