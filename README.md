# 📡 051N7-MAIL: Decentralized P2P Multi-Cast Console (v0.0.5)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Release](https://img.shields.io/badge/Release-v0.0.5-blue.svg)](https://github.com/7usr-0mail/051n7-mail/releases)
[![Network](https://img.shields.io/badge/Network-Yggdrasil_IPv6-purple.svg)](https://yggdrasil-network.github.io/)

**051N7-MAIL** is an asynchronous, end-to-end encrypted terminal mail console built for secure peer-to-peer communication across the **Yggdrasil IPv6 mesh network**. It combines OpenPGP cryptography, local virtual handle resolution (`@051n7.usr`), and a split-screen `tmux` interface.

---

## 📱 Beginner Android Quickstart & Setup Tutorial

If you are using an Android phone, follow these simple steps to get started:

### Phase 1: Install Required Phone Apps
1. Download **UserLAnd** (from Google Play Store or F-Droid) — *Provides the terminal environment where 051n7-mail runs.*
2. Download **Yggdrasil** (from Google Play Store, F-Droid, or GitHub) — *Handles the encrypted mesh VPN connection without needing a rooted phone.*

### Phase 2: Connect the Yggdrasil VPN
1. Open the **Yggdrasil VPN app** on your phone.
2. Go to **Settings / Peers** and add 2–3 public peer URIs (e.g. `tls://ygg7.mk16.de:1338`).
3. Toggle the VPN switch to **ON**.
4. **Copy your Yggdrasil IPv6 address** shown on the app home screen (starts with `200:` or `300:`).

### Phase 3: Install 051N7-MAIL (1-Command Setup)
1. Open the **UserLAnd app** and start a **Debian** or **Ubuntu** terminal session.
2. Copy and paste this single command into the terminal and press **Enter**:
   ```bash
   curl -LO https://github.com/7usr-0mail/051n7-mail/releases/download/v0.0.5/051n7-mail_0.0.5_all.deb && sudo apt update && sudo apt install -y ./051n7-mail_0.0.5_all.deb
