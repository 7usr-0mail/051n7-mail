# 📡 051N7-MAIL: Decentralized P2P Multi-Cast Console (v0.0.4)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Release](https://img.shields.io/badge/Release-v0.0.4-blue.svg)](https://github.com/7usr-0mail/051n7-mail/releases)
[![Network](https://img.shields.io/badge/Network-Yggdrasil_IPv6-purple.svg)](https://yggdrasil-network.github.io/)

**051N7-MAIL** is an asynchronous, end-to-end encrypted terminal mail console built for secure peer-to-peer communication across the **Yggdrasil IPv6 mesh network**. It combines OpenPGP cryptography, local virtual handle resolution (`@051n7.usr`), and a split-screen `tmux` interface.

---

## ✨ Features

- 🔐 **End-to-End Encryption:** All message envelopes are encrypted strictly using OpenPGP (GPG) public keys prior to transmission.
- 🌐 **Yggdrasil Mesh Native:** Communicates directly over Yggdrasil IPv6 overlays (`200::/7`), completely bypassing centralized servers or DNS resolvers.
- 📇 **Local Contacts & Handle Resolver:** Maps custom virtual handles (`alice@051n7.usr` or `squad@051n7.grp`) to GPG key identities and Yggdrasil IPv6 addresses automatically.
- ⚡ **Step-Navigable Composer:**
  - **`PREV`**: Visually erases the previous line (using ANSI terminal cursor sequences) or steps back to edit previous header fields (`TO`, `Subject`, etc.).
  - **`CANCEL`**: Instantly wipes the current form and resets composition.
  - **`CONTACTS`**: Displays your saved address book directly inside the composer.
- 🟢 **Live Real-Time Status Detection:** Continuously verifies your Yggdrasil connection and dynamically alerts you if the network goes online or offline.
- 🚀 **System Shortcuts:** Launch from anywhere with `0mail` or `7usr`.

---

## 📥 Installation

### Option 1: Debian / Ubuntu / UserLAnd (`.deb` Installer)

Download the `.deb` installer from the [Releases](https://github.com/7usr-0mail/051n7-mail/releases) page and install:

```bash
# 1. Download the latest release
curl -LO https://github.com/7usr-0mail/051n7-mail/releases/download/v0.0.4/051n7-mail_0.0.4_all.deb

# 2. Install package and dependencies
sudo apt install ./051n7-mail_0.0.4_all.deb
OF
