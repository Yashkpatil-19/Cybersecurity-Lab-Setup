# 🔐 Cybersecurity Lab Setup: VirtualBox + Kali Linux

**An isolated virtual lab for ethical hacking and penetration-testing practice.**

![VirtualBox](https://img.shields.io/badge/Hypervisor-VirtualBox%207.2-0070C0?style=flat-square)
![Kali](https://img.shields.io/badge/Kali%20Linux-2026.3-E87500?style=flat-square&logo=kalilinux&logoColor=white)
![Network](https://img.shields.io/badge/Network-10.0.0.0%2F24-238F89?style=flat-square)
![Course](https://img.shields.io/badge/Networkwalks-Week%201-C00000?style=flat-square)

---

## 📌 Overview

This project sets up a private virtual lab on a Windows laptop using **VirtualBox** and **Kali Linux**. Kali acts as the attacker machine on a custom **NAT Network**, so more VMs (targets) can be added later and tested safely.

> ⚠️ **Ethical use:** Use this lab only on systems you own or have written permission to test.

## 🎯 Objectives

- Install VirtualBox (latest) and 7-Zip
- Create a NAT Network on `10.0.0.0/24`
- Import the pre-built Kali Linux VM
- Enable shared clipboard, drag and drop, and a shared host folder
- Give Kali the static IP `10.0.0.2/24` with full Internet access
- Take a clean snapshot as a recovery point

## 🏗️ Lab Architecture

```
Windows 11 host
└── VirtualBox
    └── NatNetwork (10.0.0.0/24, gateway 10.0.0.1)
        └── Kali Linux  →  10.0.0.2/24
```

## ⚙️ Configuration

| Component | Setting |
|---|---|
| Host OS | Windows 11 |
| Hypervisor | VirtualBox 7.2 |
| Guest OS | Kali Linux 2026.3 (pre-built VirtualBox image) |
| Kali RAM / CPUs | 2048 MB / 2 |
| Virtual disk | 80.09 GB (dynamic `.vdi`) |
| Network type | NAT Network (`NatNetwork`) |
| Adapter | Intel PRO/1000 MT Desktop, Promiscuous Mode: Allow All |
| Network address | 10.0.0.0/24 (DHCP enabled) |
| Kali IP | 10.0.0.2/24 |
| Gateway | 10.0.0.1 |
| DNS | 8.8.8.8 |
| Shared folder | Host `Downloads`, auto-mounted in Kali as `sf_Downloads` |
| Clipboard / Drag and drop | Bidirectional |
| Snapshot | `Fresh Kali after installation` |

---

## 🪜 Setup Procedure

### Step 1. Install 7-Zip
Downloaded from [7-zip.org](https://7-zip.org/download.html). It is needed to extract the Kali `.7z` archive.

### Step 2. Install VirtualBox
Downloaded the latest version from [virtualbox.org](https://virtualbox.org/wiki/Downloads) and installed with default options.

> **Tip:** Newer VirtualBox starts in **Basic** mode, which hides the Network tool. Switch to **Expert** in `File > Preferences` (Ctrl+G).

### Step 3. Create the NAT Network
`File > Tools > Network > NAT Networks > Create`

```
Name:         NatNetwork
IPv4 Prefix:  10.0.0.0/24
DHCP:         Enabled
IPv6:         Disabled
```

![NAT Networks tab](1-nat-network.png)

A NAT Network lets all VMs on it talk to each other and still reach the Internet, which suits a multi-VM lab.

### Step 4. Import and configure Kali Linux
1. Downloaded the **Virtual Machines > VirtualBox** image from [kali.org/get-kali](https://kali.org/get-kali).
2. Extracted the `.7z` with 7-Zip, then double-clicked the `.vbox` file to add the VM.
3. In the VM **Settings**:
   - **Network, Adapter 1:** Attached to `NAT Network`, Name `NatNetwork`, Promiscuous Mode `Allow All`
   - **General > Features:** Shared Clipboard and Drag'n'Drop set to `Bidirectional`
   - **Shared Folders:** added the host `Downloads` folder with Auto-mount on and Make Global ticked

![VM network adapter settings](2-vm-network-adapter.png)

Default login for the pre-built image: `kali` / `kali`. The shared folder appears on the Kali desktop as `sf_Downloads`.

![Kali desktop](3-kali-desktop.png)

### Step 5. Configure the static IP in Kali
Network icon (top right) → `Edit Connections` → `Wired connection 1` → `IPv4 Settings`

```
Method:   Manual
Address:  10.0.0.2
Netmask:  24
Gateway:  10.0.0.1
DNS:      8.8.8.8
```

Then apply the connectivity fix recommended in the lab guide for newer VirtualBox and Kali versions:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

### Step 6. Verify the network

| Test | Command | Expected result |
|---|---|---|
| IP address | `ip a` | `10.0.0.2/24` on the Ethernet interface |
| Gateway | `ping -c 3 10.0.0.1` | Replies |
| Internet | `ping -c 3 8.8.8.8` | Replies |
| DNS | `ping -c 3 google.com` | Resolves and replies |
| Shared folder | `ls /media/sf_Downloads` | Host Downloads files listed |

![IP address and ping test](4-ip-and-ping-test.png)

### Step 7. Take a snapshot
Power off the VM, then `Snapshots > Take` and name it, for example `Fresh Kali after installation`.

![Snapshot](5-snapshot.png)

---

## 🐞 Problems Encountered and Fixes

**1. No Network option in VirtualBox**
The `File > Tools` menu showed only Home, Machines, Extensions, Cloud and Resources. VirtualBox was in Basic mode. Switching to Expert in Preferences made **Network** and **Media** appear.

**2. Preferences OK button disabled**
VirtualBox showed "Invalid settings detected" and greyed out OK because the default machine folder path was invalid. Setting a valid folder (`D:\VirtualBox VMs`) fixed it.

**3. Wrong default NAT network range**
The new NAT network defaulted to `10.0.2.0/24`. The lab requires `10.0.0.0/24`, so I changed the IPv4 Prefix before applying.

**4. Downloaded the wrong Kali file**
The installer ISO has no `.vbox` file. The pre-built **VirtualBox** image is the correct download for this lab.

---

## 💡 What I Learned

- Difference between NAT and NAT Network, and why NAT Network suits a multi-VM lab
- How VirtualBox network adapters connect VMs to virtual networks
- Configuring a static IP, gateway and DNS in Kali Linux
- Using snapshots as a clean recovery point before risky experiments
- Documenting problems and fixes as part of a professional project

---

## 🔗 Resources

- 7-Zip: https://7-zip.org/download.html
- VirtualBox: https://virtualbox.org/wiki/Downloads
- Kali Linux: https://kali.org/get-kali

## 👤 Author

**Yash Krishna Patil**
B.Tech Information Technology

**Program:** Cybersecurity at Networkwalks | **Week:** 1 | **Project Module:** 1, Practical Lab Environment Setup
