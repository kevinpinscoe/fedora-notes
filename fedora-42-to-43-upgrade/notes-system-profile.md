---
name: System Profile
description: Hardware, OS, disk layout, and key services on the main home server
type: user
originSessionId: ede1e2fc-66bc-4f7d-96c3-643bbfd91d37
---
## Host: kevin.kevininscoe.com

**Hardware:**
- GIGABYTE B550 AORUS ELITE AX V2 motherboard
- AMD Ryzen 5 5500 6-core
- 128 GB RAM (4x32 DDR4 G.SKILL Trident Z RGB)
- AMD Radeon PRO WX 7100 GPU
- Kingston NV3 2 TB NVMe SSD → root OS (nvme0n1)
- WD8002FZBX 8 TB Black HDD → /home (sda1)

**OS:** Fedora 42 (Adams), kernel 6.19.13-100.fc42.x86_64, x86_64
**Desktop:** KDE Plasma 6

**Disk layout:**
- nvme0n1p1 → / (root)
- nvme0n1p3 → /boot/efi
- nvme0n1p4 → /boot
- sda1 → /home (7.3T)
- nvme1n1p1 → /root_backup (mirror)
- nvme1n1p4 → /boot_backup (mirror)
- sdb1 → /home_backup (mirror)

**Backups:** ~/bin/backup.sh via systemd (rsync mirrors root→/root_backup, /boot→/boot_backup, /home→/home_backup). Excludes docker overlay2, volumes, containers dirs.

**SELinux:** enforcing (targeted policy)
**Apache:** httpd with SSL vhosts in /etc/httpd/conf.d/ssl.conf; main DNS kevin.kevininscoe.com → Tailscale IP
**Snapd:** installed and active (snaps: obs-studio, shortwave, ffmpeg-2404, core20/22/24, gnome-42/46-2204/2404)

**DNS:** kevininscoe.com CNAMEs → kevin.kevininscoe.com (Tailscale tailnet IP)
