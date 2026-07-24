# 📡 051N7-MAIL: Decentralized P2P Multi-Cast Console (v0.0.6)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Release](https://img.shields.io/badge/Release-v0.0.6-blue.svg)](https://github.com/7usr-0mail/051n7-mail/releases)
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
   curl -LO https://github.com/7usr-0mail/051n7-mail/releases/download/v0.0.6/051n7-mail_0.0.6_all.deb && sudo apt update && sudo apt install -y ./051n7-mail_0.0.6_all.deb
   ```

   > **UserLAnd note:** `apt install ./<file>.deb` auto-installs the dependencies. If you
   > install with `dpkg -i` directly instead, run it with `sudo` (dpkg needs superuser on UserLAnd).

   *(Latest builds are always on the [Releases page](https://github.com/7usr-0mail/051n7-mail/releases).)*

### Phase 4: Create Your Encryption Key (One-Time Setup)
1. Generate your GPG key instantly in UserLAnd (bypasses Android pinentry/entropy hangs):
   ```bash
   gpg --batch --passphrase '' --quick-gen-key "yourname@051n7.usr" default default
   ```
2. Export your public key to share with contacts:
   ```bash
   gpg --armor --export "yourname@051n7.usr" > my_public_key.asc
   ```
3. Import a contact's public key when they send you theirs:
   ```bash
   gpg --import friend_public_key.asc
   ```

### Phase 5: Launch Your Mail Station
1. Ensure your Yggdrasil VPN app is **ON**.
2. Type either shortcut command in UserLAnd terminal:
   ```bash
   0mail
   ```
   *(or `7usr`)*
3. On the first launch, paste your **Yggdrasil IPv6 Address** (from Phase 2) when prompted!

---

## ✨ Features

- 🔐 **End-to-End Encryption:** All message envelopes are encrypted strictly using OpenPGP (GPG) public keys prior to transmission.
- 🌐 **Yggdrasil Mesh Native:** Communicates directly over Yggdrasil IPv6 overlays (`200::/7`), completely bypassing centralized servers or DNS resolvers.
- 📇 **Folder-Based Contact Directory:** Maps custom handles (`alice@051n7.usr` or group directories `squad@051n7.grp/`) to GPG key identities and Yggdrasil IPv6 addresses automatically.
- ⚡ **Step-Navigable Composer:**
  - **`PREV`**: Visually erases the previous line or steps back to edit previous header fields (`TO`, `Subject`, etc.).
  - **`CANCEL`**: Instantly wipes the current form and resets composition.
  - **`CONTACTS`**: Displays your saved address book directly inside the composer.
  - **`QUEUE`**: Inspects pending outbound messages sitting in the spool queue.
- 📬 **Outbound Spool Retry Worker:** Background worker retries sending queued packets every 10 seconds until the recipient comes online.
- 🟢 **Live Real-Time Status Detection:** Continuously verifies your Yggdrasil connection and dynamically alerts you if the network goes online or offline.
- 🚀 **System Shortcuts:** Launch from anywhere with `0mail` or `7usr`.

---

## 🕹️ Composer Controls

When composing a message in the bottom panel:

| Command | Action |
| :--- | :--- |
| **`CONTACTS`** | Type at `TO` prompt to view your saved local address book. |
| **`QUEUE`** | Type at `TO` prompt to inspect pending outbound messages in spool. |
| **`PREV`** | Type on any prompt or body line to erase the previous line and step backward. |
| **`CANCEL`** | Type at any prompt or body line to discard the message and reset the form. |
| **`END`** | Type on a clean line when finished writing body text to encrypt and dispatch. |

---

## 📇 Handles & Contact Directory

You can address recipients using virtual email handles:
* **Individuals:** `handle@051n7.usr` (saved as individual `.usr` files)
* **Groups / Multicast:** `group@051n7.grp/` (saved as folders containing member `.usr` files)

When sending to a new recipient, the composer will prompt to save them to your local address book (`~/.051n7_mail/contacts/`). Future messages can be sent simply by typing their handle!

---

## 📂 System File Hierarchy

All application data is stored locally in your home directory:

```text
~/.051n7_mail/
├── inbox/           # Incoming encrypted .gpg message packets
├── archive/         # Decrypted offline message storage
├── spool/           # Outbound transmission spool (.gpg + .meta)
├── contacts/        # Directory containing .usr files and .grp folders
└── my_ygg_ip        # Saved local Yggdrasil IPv6 address
```

---

## 🤖 Acknowledgements & AI Development

This project was architected, written, debugged, and packaged with the interactive pair-programming assistance of AI assistants (**Google Gemini**, **Qwen**, and **Arena.ai Agent Mode**).

### Sourced Open-Source Projects & Libraries
- **[Yggdrasil Network](https://yggdrasil-network.github.io/):** Fully encrypted end-to-end P2P IPv6 overlay network routing.
- **[GnuPG (GPG)](https://gnupg.org/):** OpenPGP standard implementation for cryptographic envelope encryption.
- **[tmux](https://github.com/tmux/tmux):** Terminal multiplexing for the dual-panel monitor and composer layout.
- **[Python 3](https://www.python.org/):** Asynchronous socket listening, handle parsing, and packaging automation.
- **[Debian Policy](https://www.debian.org/doc/debian-policy/):** Package layout standard for `.deb` distributions.

---

## 📜 License

Distributed under the [MIT License](LICENSE).
