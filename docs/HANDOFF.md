# 051N7-MAIL — PROJECT HANDOFF (for a new chat)

Paste this whole file at the start of a new chat to restore full context.
Last updated: 2026-07-24, right after shipping v0.0.6-beta.

═══════════════════════════════════════════════════════════════════════
## 1. WHAT THE PROJECT IS
═══════════════════════════════════════════════════════════════════════
051n7-mail: a Bash terminal "mail" console for decentralized, GPG-encrypted
P2P messaging over the Yggdrasil IPv6 mesh. Single monolithic bash script
(~727 lines). Runs on ANDROID via UserLAnd (no root). Launched by shortcuts
`0mail` / `7usr`. GitHub repo: 7usr-0mail/051n7-mail (branch main).
Leetspeak: 051n7 = "OSINT". Goal: a genuinely safe, well-designed tool.

Two tmux panes: run_receiver (monitor + python listener on port 8525 +
spool retry worker) and run_composer (the interactive TUI to write/send).
Transport: GPG-encrypt per recipient -> nc -6 to peer's Ygg IPv6 :8525.
Contacts: ~/.051n7_mail/contacts/  (.usr files, .grp folders of members).

═══════════════════════════════════════════════════════════════════════
## 2. CURRENT STATE (as of v0.0.6-beta, SHIPPED & CI GREEN)
═══════════════════════════════════════════════════════════════════════
- v0.0.6 released on GitHub (tag v0.0.6, release title "v0.0.6-beta", pre-release).
- CI (GitHub Actions ludeeus/action-shellcheck) is GREEN — code is ShellCheck-clean.
- Installed on device via .deb, dpkg -l shows: ii 051n7-mail 0.0.6.
- Clean 0.0.6 script sha256: 064affce03765beb53ae758d96da829c7bc07035da0ed89ffb8965722b3fe2fb
- Live binary at /usr/bin/051n7-mail (0mail/7usr symlink here). /usr/local/bin
  copy was retired; a backup kept as ~/051n7-mail.usrlocal-backup-*.

### What 0.0.6 fixed
- B8 SECURITY: contact files were `source`d (arbitrary code execution). Replaced
  with safe parser `_051n7_read_contact` (reads only GPG_ID / YGG_IP, executes nothing).
- FROM validation v2: only <name>@051n7.usr / @051n7.grp accepted; bare name -> .usr;
  reject 0mail/peek handles, .dev domain, and foreign domains (e.g. name@gmail.com).
  Uses FULL-domain match (${mail_from#*@} == "051n7.usr"/"051n7.grp").
- B2 multicast: To: header now per-recipient (was always GPG_KEYS[0]).
- ShellCheck: fixed all findings (read -r, removed unused ygg_to/to_buffer/dummy->_dummy,
  split declare+assign, mapfile/quote arrays, $? -> direct if, useless cat -> redirection).
- Kept the TO-prompt wording "[e.g. alice, peek]" (user preference).

═══════════════════════════════════════════════════════════════════════
## 3. USER'S ENVIRONMENT & QUIRKS (CRITICAL — affects every deliverable)
═══════════════════════════════════════════════════════════════════════
- Platform: UserLAnd (Android Linux, proot, NO ROOT).
- FILE TRANSFER between assistant and device: assistant writes a file into the
  workspace, presents it in the VIEWER; user saves it into ~/ftp/ (proftpd is how
  they move files phone<->userland; sdcard is restricted). Verify transfers with sha256sum.
- User's chat CHARACTER LIMIT is small (~1800-2500 chars). DO NOT paste huge blocks
  or long base64 in one message; the FULL-FILE viewer-save method is the reliable path.
- Pasting multi-line scripts at the $ prompt breaks (read prompts eat the paste).
  => Always deliver as "save file from viewer, then run", NOT raw paste.
- `dpkg -i` requires sudo on UserLAnd.
- PROOT TAR IS BROKEN: `tar -c` (create archives) FAILS with "Cannot stat:
  Function not implemented" — even on a single file. `tar -x` (extract) works.
  => For reversible cleanup use a TRASH FOLDER (mv to ~/.051n7_trash/<ts>/), NOT tar.
  => The .deb MUST be built proot-safe: `tar --owner=0 --group=0 -C <dir> .` for
     control.tar.gz + data.tar.gz, then `ar rc file.deb debian-binary control.tar.gz
     data.tar.gz`. Plain `dpkg-deb --build` FAILS on device (chown via tar).
- git push: user has token; enabled `git config --global credential.helper store`
  so token entered once. Repo remote: https://github.com/7usr-0mail/051n7-mail

═══════════════════════════════════════════════════════════════════════
## 4. RELEASE CONVENTIONS (user preferences)
═══════════════════════════════════════════════════════════════════════
- Git TAG = plain version, e.g. v0.0.7.  Release NAME/title = append -beta (v0.0.7-beta),
  mark as pre-release.
- CHANGELOG: keep LEAN — only current release's notes (user drops old entries).
- Release description must END with: "Full Changelog: https://github.com/7usr-0mail/051n7-mail/commits/Pre-release"
- Release via gh CLI: gh release create v0.0.X <deb> <SHA256SUMS> --repo 7usr-0mail/051n7-mail
  --title "v0.0.X-beta" --notes-file <notes> --prerelease --verify-tag
- After each release: regenerate SHA256SUMS (of .deb + script), update README version
  refs + install URL, bump DEBIAN/control Version.
- STANDING RULE: at end of each release cycle, OFFER a scratch cleanup (trash-folder style).
- Don't fix bugs and refactor at the same time. Ship small, keep CI green.

═══════════════════════════════════════════════════════════════════════
## 5. HOME DIR LAYOUT (after final cleanup)
═══════════════════════════════════════════════════════════════════════
~/051n7-repo/        git repo (source of truth): 051n7-mail, README.md, CHANGELOG.md,
                     SECURITY.md, LICENSE, SHA256SUMS, .github/workflows/test.yml
~/051n7-mail-pkg/    .deb build tree: DEBIAN/{control,postinst,postrm}, usr/bin/051n7-mail,
                     usr/share/applications/051n7-mail.desktop
~/051n7-improved/    0.0.7+ feature SOURCE (see section 7): receiver.py, sign_message.sh,
                     verify_message.sh, load_config.sh, show_keys.sh, settings.conf
~/ftp/               transfer folder (viewer-saved files land here)
~/051n7-mail_0.0.6_all.deb   released artifact
~/051n7-mail.usrlocal-backup-*   old binary backup
~/.051n7_mail/  ~/.051n7_ygg/   live tool data
postinst symlinks 0mail/7usr -> /usr/bin/051n7-mail

═══════════════════════════════════════════════════════════════════════
## 6. NEXT-UP: 0.0.7 PLAN (recommended split so we don't run out of tokens)
═══════════════════════════════════════════════════════════════════════
Ship in SMALL chunks, each its own release, CI green each time:

0.0.7  = SPOOL FIXES (the bug the user actually hit):
  SP1 (MAIN): retry worker run_spool_retry is a child of run_receiver and gets KILLED
      by the SIGINT/SIGTERM trap on tmux kill-session -> queue never drains after app
      closes. User's repro: PEEK with VPN off -> spooled; VPN on -> stays queued even
      after kill-session. FIX: startup one-shot flush + keep worker; or decouple it.
      (No root/systemd on UserLAnd -> prefer startup flush + manual flush command.)
  SP2: add a MANUAL "flush/retry queue now" command (e.g. QUEUE FLUSH at TO prompt).
  SP3: retry worker success msgs are silenced (>/dev/null) -> surface deliveries.

0.0.8  = TO/FROM usability:
  TF1: FROM must NOT allow @051n7.grp (sending "from a group" is meaningless) — restrict to .usr.
  TF2: TO shortcut expansion: ace.u/ace.usr -> ace@051n7.usr ; squad.g/squad.grp -> squad@051n7.grp
       (CONFIRMED mapping; .grp -> .grp). Bare name still -> .usr.

0.0.9  = GPG SIGNING + VERIFICATION (from ~/051n7-improved/sign_message.sh & verify_message.sh):
  currently only ENCRYPTS. Add --sign so recipients verify sender authenticity; show
  Good/untrusted/no-signature on receive. Makes FROM-spoofing cryptographically moot.

0.1.0  = ghost anonymous sender + install wizard (051n7-mail --setup) + spool hardening
  SP4 (PEEK IP fragile on UserLAnd: yggdrasilctl has no perms -> falls back to own IP/::1),
  SP5 (retry limit + backoff + expiry), SP6 (atomic spool: .gpg made before .meta -> orphan risk).
  Also consider hardened receiver.py + settings.conf config file from ~/051n7-improved/
  (NOTE: load_config.sh currently `source`s settings.conf -> same B8 risk, harden it).
  Architecture: do the monolith->modular split DELIBERATELY here (051n7-improved is a
  half-done split; main script doesn't call the helpers yet).

═══════════════════════════════════════════════════════════════════════
## 7. KNOWN EXISTING FEATURES (don't accidentally remove them)
═══════════════════════════════════════════════════════════════════════
PEEK echo self-test (peek@051n7.dev, PONG detection), transmission diagnostics
(ERR-LOC-01/NET-02/RCV-03), QUEUE / QUEUE CLEAR spool commands, multicast via .grp,
contacts save (individual/group), spool auto-retry (buggy, see SP1), VPN status via
pinging OWN stored Ygg IP (valid on UserLAnd: that IP only exists while VPN up).

═══════════════════════════════════════════════════════════════════════
## 8. HOW TO RESUME IN THE NEW CHAT
═══════════════════════════════════════════════════════════════════════
1. Paste this file.
2. To let the assistant see the real current code, upload the repo files. Safe method:
   base64 the repo (NOT ~; exclude secrets) into a .txt and upload it, OR viewer-save
   individual files. NEVER upload .gnupg/ (private keys), .git/, .bash_history, or
   ~/.git-credentials. (Earlier a throwaway GPG key was uploaded — it was a test key.)
3. Say which chunk to start (recommended: 0.0.7 spool SP1+SP2).
