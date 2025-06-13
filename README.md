# 🍓 Raspberry Pi 4 Home NAS with Samba (Password-Protected)

This project turns your **Raspberry Pi 4 Model B** into a secure **home file server (NAS)** using **two external SSDs** and **Samba**, accessible across your Wi-Fi network from **Windows, macOS, or Linux** devices — all protected with a username and password.

---

## 📷 Setup Pictures

| ![Hardware Setup](images/setup-hardware.jpeg) | ![Access on macOS](images/mac-smb-access.png) |
|---------------------------------------------|-----------------------------------------------|
| *Raspberry Pi with mounted SSDs*            | *Access via Finder on Mac using SMB*          |

---

## 📦 Requirements

- Raspberry Pi 4 Model B (Raspberry Pi OS installed)
- Two portable SSDs (formatted: exFAT, NTFS, or ext4)
- Power supply and USB ports for SSDs
- Local home Wi-Fi or Ethernet network
- Monitor/keyboard or SSH access to your Pi

---

## 🛠️ Step by Step Setup

### 🔄 1. Update Your Raspberry Pi

```bash
sudo apt update && sudo apt upgrade -y
```
---

### 💽 Step 2: Connect and Mount SSDs

1. Plug in your SSDs

2. Check devices

```bash
lsblk
```
you will likely see:

* /dev/sda1 for SSD1
* /dev/sdb1 for SSD2

3. Create mount points

```bash
sudo mkdir -p /mnt/ssd1
sudo mkdir -p /mnt/ssd2
```

4. Mount manually 

```bash
sudo mount /dev/sda1 mnt/ssd1
sudo mount /dev/sdb1 mnt/ssd2
```

5. Get UUIDs for permanent mounting

```bash
sudo blkid
```

Example output:
```bash
/dev/sda1: UUID="abcd123"
/dev/sdb1: UUID="efgh456"
```

6. Edit /etc/fstab
```bash

sudo nvim /etc/fstab
```
> you can use the editor of your choice like Nano

Add these at the bottom of the file:

```ini
UUID=abcd123 /mnt/ssd1 auto defaults,nofail 0 0
UUID=efgh456 /mnt/ssd2 auto defaults,nofail 0 0
```

7. Reboot to test the changes

```bash
sudo reboot now
```

after reboot check for mounts:

```bash
ls /mnt/ssd1
ls /mnt/ssd2
```

---

### 🔐 Step 3: Set Correct Permissions

```bash
sudo chmod -R pi:pi /mnt/ssd1
sudo chmod -R pi:pi /mnt/ssh2
```

### 📥 Step 4: Install Samba

```bash
sudo apt install samba -y
```

### 🔒 Step 5: Create Samba User

Use your Raspberry Pi's user(it's usually **pi**):

find your username:
```bash
whoami
```

Now use it for Samba:
```bash
sudo smbpasswd -a pi
sudo smbpasswd -e pi
```

* Set a password when the promp comes in.
* this password will be used to access the shared folders.

### 🧩 Step 6: Configure Samba Shares

Edit the Samba config file:

```bash
sudo nvim /etc/samba/smb.conf
```

* Add the following at the end of the file:

```ini
[SSD1]
   path = /mnt/ssd1
   browseable = yes
   writable = yes
   valid users = pi
   create mask = 0775
   directory mask = 0775

[SSD2]
   path = /mnt/ssd2
   browseable = yes
   writable = yes
   valid users = pi
   create mask = 0775
   directory mask = 0775
```
> make sure you match your drive path and username 

Save and exit.

### 🔁 Step 7: Restart Samba

```bash
sudo systemctl restart smbd
```

---

### 🌐 Access from Other Devices

💻 From Windows

1. Open File Explorer
2. Enter in address bar:

```lua
\\raspberrypi.local
```

or if *.local* doesnt work you can use your Pi's IP:

```bash
hostname -I
```

3. Enter login credentials:

* Username: pi >or the username you created
* Password: (the *smbpasswd* you set earlier)

🍏 From macOS

1. Open Finder --> GO --> Connect to Server

2. Enter:
```bash
smb://raspberrypi.local
```

3. Login with your credentials:

* Username: pi
* Password: (your Samba password)

---