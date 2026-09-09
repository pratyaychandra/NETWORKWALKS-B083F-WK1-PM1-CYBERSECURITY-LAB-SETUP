<div align="center">

# 🛡️ Kali Linux Lab Setup

**A Self-contained Isolated Pentesting Sandbox Built From Scratch On VirtualBox.**

![VirtualBox](https://img.shields.io/badge/VirtualBox-v7.2-183A61?style=for-the-badge&logo=virtualbox)
![Kali](https://img.shields.io/badge/Kali%20Linux-2026.2-557C94?style=for-the-badge&logo=kalilinux)
![Network](https://img.shields.io/badge/Subnet-10.0.0.0%2F24-black?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge)

</div>

---

## 📖 Table of Contents
- [Overview](#-overview)
- [Network Architecture](#-network-architecture)
- [Lab Specs](#-lab-specs)
- [Build Log](#-build-log)
- [Verification Tests](#-verification-tests)
- [Troubleshooting Journal](#-troubleshooting-journal)
- [Key Takeaways](#-key-takeaways)
- [Roadmap](#-roadmap)

---

## 🎯 Overview

This isn't just a "VM installed" screenshot dump, it's a documented build of an **SANDBOXED ENVIRONMENT**: a private network where an attacker machine (Kali) can be safely used to practice reconnaissance, scanning and exploitation techniques without ever touching the outside world unintentionally.

> ⚠️ **Ethics Notice:** This lab is strictly for authorized, personal and educational use. No tools here were used against systems I don't own or have permission to test.

---

## 🗺️ Network Architecture

```mermaid
graph TD
    A[Host Machine<br/>Windows 10/11] --> B[VirtualBox Hypervisor v7.2]
    B --> C[NAT Network: NatNetwork<br/>10.0.0.0/24]
    C --> D[Kali Linux 2026.2<br/>10.0.0.2/24 - Attacker]
    C -.future.-> E[Target VM Slot<br/>10.0.0.3 - 99]
    D --> F[Internet via NAT<br/>DNS: 8.8.8.8]

    style D fill:#557C94,stroke:#333,color:#fff
    style C fill:#111,stroke:#f00,color:#fff
```

Any future VM can be added into the same `10.0.0.0/24` range.

---

## ⚙️ Lab Specs

| Layer | Detail |
|---|---|
| 🖥️ Host OS | Windows 11 |
| 🧰 Hypervisor | VirtualBox 7.2 |
| 🐉 Guest OS | Kali Linux 2026.2 |
| 🧠 Allocated RAM | 4096 MB |
| 🌐 Network Mode | NAT Network (isolated and internet capable) |
| 📡 Subnet | `10.0.0.0/24` |
| 🐧 Kali Static IP | `10.0.0.2/24` |
| 🚪 Gateway | `10.0.0.1` |
| 🌍 DNS | `8.8.8.8` |
| 📁 Shared Folder | Host `/Downloads` → Kali |
| 🎛️ Clipboard / Drag-Drop | Bidirectional, enabled |

---

## 🪜 Build Log

<details>
<summary><b>Step 1 — Hypervisor & Tooling</b></summary>

Installed 7-Zip (for archive extraction) and VirtualBox 7.2 as the hypervisor.
</details>

<details>
<summary><b>Step 2 — Isolated NAT Network</b></summary>

Created a dedicated NAT Network instead of default NAT — this allows multiple future VMs to talk to each other *and* reach the internet.

```
Name:        NatNetwork
IPv4 Prefix: 10.0.0.0/24
DHCP:        Enabled
IPv6:        Disabled
```

![NAT Network Setup](docs/screenshots/01-nat-network.png)
</details>

<details>
<summary><b>Step 3 — Importing Kali Linux</b></summary>

Downloaded Kali Linux 2026.2 (official VirtualBox image) and attached it to `NatNetwork`.

![Kali VM Adapter](docs/screenshots/02-kali-vm-adapter.png)
![Kali Desktop](docs/screenshots/03-kali-desktop.png)
</details>

<details>
<summary><b>Step 4 — Static IP Configuration</b></summary>

```bash
IP Address:  10.0.0.2
Netmask:     255.255.255.0
Gateway:     10.0.0.1
DNS:         8.8.8.8
```

![Static IP Config](docs/screenshots/04-static-ip-config.png)
</details>

<details>
<summary><b>Step 5 — Shared Folder & Clipboard</b></summary>

Enabled bidirectional clipboard, drag-and-drop, and mapped host `/Downloads` as a permanent shared folder.

![Shared Folder](docs/screenshots/06-shared-folder.png)
</details>

<details>
<summary><b>Step 6 — Baseline Snapshot</b></summary>

Took a clean snapshot (`Clean-Kali-NetworkSetup`) as a recovery checkpoint before any future exploitation practice.

![Snapshot](docs/screenshots/07-snapshot.png)
</details>

---

## ✅ Verification Tests

| Test | Command | Result |
|---|---|---|
| Interface & IP | `ip a show eth0` | ✅ `10.0.0.2/24` confirmed |
| Gateway reachability | `ping -c 2 10.0.0.1` | ✅ Replies received |
| Internet reachability | `ping-c 2 8.8.8.8` | ✅ Replies received |
| DNS resolution | `nslookup networkwalks.com` | ✅ Resolved |


![Connectivity Test](docs/screenshots/05-connectivity-test.png)

---

## 🐞 Troubleshooting Journal

**Issue:** Internet dropped after switching to a static IP (common on Kali 2026.x + VirtualBox 7).

**Root cause:** NetworkManager's DAD (Duplicate Address Detection) timeout stalling the connection.

**Fix:**
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

---

## 💡 Key Takeaways

- **NAT vs. NAT Network** — a NAT Network lets multiple VMs cross-communicate *and* reach the internet, unlike standard per-VM NAT. Essential for multi-machine labs.
- Static IP + documented subnet ranges make future target VMs trivial to add.
- Snapshots aren't optional — they're the "undo button" for a lab you're about to break on purpose.

---

## 🔭 Roadmap

- [ ] Add Metasploitable2 as a vulnerable target (`10.0.0.5`)
- [ ] Add Windows 10 target VM (`10.0.0.10`)
- [ ] Run first Nmap scan across the subnet
- [ ] Document first exploitation walkthrough

---

<div align="center">

Built as part of **NetworkWalks Academy — Cybersecurity Internship, Week 1**

</div>
