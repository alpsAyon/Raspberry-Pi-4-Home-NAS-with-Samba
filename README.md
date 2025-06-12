# 🍓 Raspberry Pi 4 Home NAS with Samba (Password-Protected)

This project turns your **Raspberry Pi 4 Model B** into a secure **home file server (NAS)** using **two external SSDs** and **Samba**, accessible across your Wi-Fi network from **Windows, macOS, or Linux** devices — all protected with a username and password.

---

## 📷 Setup Example (suggested images)

| ![Hardware Setup](images/setup-hardware.jpeg) | ![Access on macOS](images/mac-smb-access.png) |
|---------------------------------------------|-----------------------------------------------|
| *Raspberry Pi with mounted SSDs*            | *Access via Finder on Mac using SMB*          |

---

## 📦 Requirements

- Raspberry Pi 4 Model B (Raspberry Pi OS installed)
- Two portable SSDs (formatted: exFAT, NTFS, or ext4)
- Power supply and USB ports for SSDs (use a powered USB hub if needed)
- Local home Wi-Fi or Ethernet network
- Monitor/keyboard or SSH access to your Pi

---

## 🛠️ Step-by-Step Setup

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

---