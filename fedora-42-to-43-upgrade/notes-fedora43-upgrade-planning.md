---
name: Fedora 43 Upgrade Plan
description: In-progress planning for Fedora 42 → 43 upgrade; research findings and outstanding tasks
type: project
originSessionId: ede1e2fc-66bc-4f7d-96c3-643bbfd91d37
---
**Goal:** Upgrade from Fedora 42 to Fedora 43. Planning started 2026-04-25. Upgrade targeted for the week of 2026-04-28.

**Why:** Routine major release upgrade. Fedora 43 was released 2025-10-28, so it has been out ~6 months.

## Work completed this session (2026-04-25)

- System inventory done (see system_profile.md and container_inventory.md)
- Fedora 43 research completed (key findings below)
- FEDORA-UPGRADE-43.md checklist file — **CREATED 2026-04-26** at /home/kinscoe/FEDORA-UPGRADE-43.md
- RUNBOOK.md files for missing containers — NOT YET CREATED

## Outstanding tasks (next session)

1. **Create /home/kinscoe/FEDORA-UPGRADE-43.md** — main upgrade checklist (see research findings below)
2. **Create RUNBOOK.md** for these 7 containers — **DONE 2026-04-26**: actualbudget, excalidraw, karakeep, n8n, pastebooks, wikijs, youtrack
3. **Add Fedora upgrade section** to all existing RUNBOOK.md files — **DONE 2026-04-26** (convertx, filestash, garage, gitea, glean, home_file_server, kroki, openbao, rsshub, woodpecker-ci)
4. **Add Kasm upgrade notes** — Kasm 1.18.1 at /opt/kasm has its own upgrade path (not docker-compose)

## Key Fedora 43 research findings

### Breaking changes
- **DNF 5** replaces DNF4/YUM/dnf-automatic — separate package history DB; packages installed via dnf4 won't appear in dnf5 history
- **RPM 6.0** — major new version; multiple key signing
- **glibc 2.42** — removed ancient `<termio.h>` / `struct termio`; use `<termios.h>`
- **python-nose** retired (no replacement)
- **GNOME Wayland-only** (X11 deprecated) — less relevant since desktop is KDE, but worth noting
- **GCC 15.2, Python 3.14, LLVM 21**

### Docker/container concerns
- Podman 5.x is default; Docker still works via docker-ce (RPM Fusion or docker.com repo)
- No known container-breaking changes from F42→F43 for docker-ce
- **Kasm 1.18.1** must be checked for F43 compatibility — Kasm has its own upgrade procedure

### SELinux
- `label:disable` containers (karakeep, n8n) may need revalidation post-upgrade
- SELinux policy updated (selinux-policy-minimum-42.12-1.fc43)

### Snapd
- snapd 2.71 available for F43 — should upgrade fine; no breaking changes documented

### AMD GPU (Radeon WX 7100)
- Open-source amdgpu driver only (no official AMD proprietary support for Fedora)
- Should be fine — already using amdgpu on F42

### Kernel
- F43 ships Linux 6.17
- Some users report boot failures with kernel 6.17.8+ (Framework laptop / GNOME specific)
- Keep older kernel entries in GRUB as fallback

### Commonly reported upgrade failures
- Wine package conflicts (may need `dnf remove wine` first)
- Qt6/Dolphin failures — relevant since KDE Plasma 6 is the desktop
- RPM Fusion packages causing dependency conflicts during upgrade
- Upgrades hanging at ~42% (NVIDIA akmods; not relevant here — AMD GPU)
- Some users unable to boot after kernel 6.17.8 upgrade

### Backup strategy during upgrade
- backup.sh syncs /→/root_backup, /boot→/boot_backup, /home→/home_backup
- backup.sh excludes /var/lib/docker overlay2/volumes — docker data NOT in system backup
- **Run all container backup timers manually before upgrade**
- **Snapshot /opt/containers tree** separately (not covered by backup.sh)

### Key pre-upgrade steps to capture in checklist
1. Run backup.sh manually; verify all container backup timers ran recently
2. Snapshot /opt/containers (rsync to /home_backup or similar)
3. `dnf upgrade --refresh` to fully update F42 first
4. Check RPM Fusion and other 3rd party repos for F43 availability
5. Remove or handle conflicting packages (wine, any akmods)
6. Note Kasm upgrade path (separate from dnf system upgrade)
7. ~~Verify woodpecker-ci agent restart loop is resolved before upgrade~~ **FIXED 2026-04-26**: root cause was `v3` rolling tag pointing to a broken `next` dev build + missing `WOODPECKER_BACKEND=docker` + needed `security_opt: label:disable` for Docker socket SELinux access. Pinned to `v3.7.0`.
8. After upgrade: verify all container services restart cleanly
9. After upgrade: re-test SELinux labels on containers using label:disable
10. After upgrade: validate certbot/TLS renewal (certbot-renew.timer)
