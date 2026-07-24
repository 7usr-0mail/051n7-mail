# 051n7-mail — Roadmap

## 0.0.6 (THIS release) — monolith, bug/security fixes only
- [x] B2: multicast To: header per-recipient
- [x] B8: safe contact reader (no more `source` of contact files) -> _051n7_read_contact
- [x] FROM validation: ONLY .usr and .grp accepted; 0mail/peek/.dev rejected
- [x] M1: robust RAND_HEX
- [ ] Packaging: version -> 0.0.6, drop 4.0.0 artifact, CHANGELOG, rebuild .deb, SHA256SUMS, tag

## 0.0.7 (NEXT release) — add GPG signing + ghost anonymous sender
- GPG SIGNING + VERIFICATION (from 051n7-improved/sign_message.sh, verify_message.sh)
- `ghost` anonymous-sender feature (check both 699 and improved for best impl)
1. GPG SIGNING + VERIFICATION (sign_message.sh / verify_message.sh)
   - Currently tool only ENCRYPTS. Add --sign so recipients can verify sender authenticity.
   - Makes FROM-spoofing cryptographically irrelevant. Highest-value feature.
   - Show "Good signature" / untrusted / no-signature status on receive.
2. HARDENED RECEIVER (receiver.py)
   - bind "::", per-conn 30s timeout, logging, per-connection try/except so one bad
     packet doesn't crash the listener. Replaces the fragile inline python in run_receiver.
3. CONFIG FILE (settings.conf + load_config.sh)
   - user-tunable: LISTEN_PORT, CONNECTION_TIMEOUT, RETRY_INTERVAL, MAX_RETRIES,
     AUTO_ARCHIVE, VERBOSE_LOGGING, DEFAULT_FROM, SIGNING_KEY, paths.
   - Solves packaging P2 (hard-coded 8525).
   - NOTE: load_config.sh currently `source`s the conf -> harden like B8 (lower risk: own file).
4. KEY MANAGEMENT (show_keys.sh)
   - list secret/public keys, help export public key.

## Architecture decision for 0.1.0
- Do the monolith -> modular split deliberately, AFTER 0.0.6 ships clean.
- Decide install layout for helpers (e.g. /usr/lib/051n7-mail/ + main script sources them,
  or keep everything embedded). For UserLAnd (no root) embedding may still be simplest.

## 0.0.7 — SPOOL / QUEUE BUGS (found by user's PEEK-with-VPN-off test)
Symptom reported: PEEK sent with VPN OFF -> correctly spooled; turned VPN ON ->
message STAYS in queue and never delivers, even after `tmux kill-session`.

Root-cause analysis (confirmed by reading 0.0.6 code):
- [ ] SP1 (MAIN): retry worker `run_spool_retry` runs as a background child of
      run_receiver, and the SIGINT/SIGTERM trap (line ~111) KILLS it on kill-session.
      So once the app/monitor closes, NOTHING drains the queue. Retry only happens
      while the monitor pane is alive.
      FIX ideas: (a) decouple the retry worker from the receiver / make it survive,
      or (b) run a one-shot "flush queue" on startup, or (c) systemd-user/cron-like
      loop. On UserLAnd (no root/systemd) prefer a startup flush + manual flush cmd.
- [ ] SP2: no MANUAL "flush/retry queue now" command. User can't force a resend
      after VPN returns. Add a composer/menu action e.g. `QUEUE FLUSH`.
- [ ] SP3: retry worker success messages are silenced (`run_spool_retry >/dev/null 2>&1 &`,
      line ~108) so delivered-from-queue events are invisible. Surface them.
- [ ] SP4: PEEK target IP resolution fragile on UserLAnd. get_peek_ip() tries
      `yggdrasilctl getPeers` (no perms on UserLAnd -> empty) then falls back to own
      IP file, else `::1`. PEEK self-loop may point at an address that isn't reliably
      deliverable. Revisit PEEK addressing for the no-root case.
- [ ] SP5: no retry limit / no backoff -> dead-peer messages retry every 10s forever
      and clutter the queue. Add max-retries + exponential backoff + optional expiry.
- [ ] SP6: orphan risk - .gpg is created before the nc attempt and .meta written only
      on failure; a crash in between can leave a .gpg with no .meta (never retried).
      Worker cleans .meta-without-.gpg but not the reverse. Make spooling atomic.

Priority for 0.0.7: SP1 + SP2 (make the queue actually drain + let user trigger it),
then SP3 (visibility). SP4/SP5/SP6 are hardening.

## 0.0.7 — TO / FROM usability + validation (user requests)
- [ ] TF1: FROM must NOT allow @051n7.grp. Sending "from a group" is meaningless.
      Change v2 FROM validation to accept ONLY <name>@051n7.usr (and bare->.usr,
      and ghost@051n7.usr). Reject .grp as a sender. (Currently line ~360 allows .grp.)
- [ ] TF2: TO shortcut expansion for faster typing:
        ace.u   -> ace@051n7.usr
        ace.usr -> ace@051n7.usr
        squad.g   -> squad@051n7.grp
        squad.grp -> squad@051n7.grp   (NOTE: user's msg wrote "squad.grp -> squad@051n7.usr"
                                        -- almost certainly a typo; .grp should map to .grp.
                                        CONFIRM with user before implementing.)
      Implement as a small normalize function applied to the TO handle before lookup_contact.
      Keep existing behaviour: bare name with no suffix still -> @051n7.usr.
- Searched base(699)/improved/repo: NO existing shortcut-expansion code found; build fresh.

## 0.0.6 STATUS: BUILT + INSTALLED SUCCESSFULLY (2026-07-24)
- dpkg -i works on UserLAnd (requires `sudo`). Note in README install docs.
- .deb must be built proot-safe: tar --owner=0 --group=0 + ar (NOT plain dpkg-deb).
- installed binary hash b60b834985f3ea...c175d9 confirmed live at /usr/bin/051n7-mail.
- Remaining for release: git commit + tag v0.0.6 + push; GitHub Release with .deb + SHA256SUMS.

## 0.0.7 — Install wizard (user request)
- [ ] WZ1: interactive first-run/install wizard (pairs with 051n7-improved config work):
      guide user through: enter Yggdrasil IPv6, GPG key gen/import, default FROM,
      port/timeout settings (settings.conf), verify deps present. Reduce the manual
      README steps (Phase 4/5) into a guided flow. Consider a `051n7-mail --setup`.

## WORKFLOW NOTES (user's environment - remember for all future deliverables)
- FILE TRANSFER: user saves every viewer file into ~/ftp/ (proftpd is how they move
  files between UserLAnd and the phone; sdcard access is restricted on their device).
  So reference ~/ftp/<file> in instructions, and viewer-save remains the transfer method.
- dpkg -i requires `sudo` on UserLAnd.
- .deb MUST be built proot-safe (tar --owner=0 --group=0 + ar), not plain dpkg-deb.

## RELEASE CONVENTIONS (user preferences)
- Git TAG:  v0.0.6      (plain version tag)
- Release NAME/title:  v0.0.6-beta   (append -beta to the display name, not the tag)
- CHANGELOG: keep it lean - only the CURRENT release's notes; drop old/irrelevant
  entries (user removed v0.0.5 and v0.0.1 details as not relevant).
- Every GitHub release description should END with a full-changelog link, e.g.:
    Full Changelog: https://github.com/7usr-0mail/051n7-mail/commits/Pre-release
- Prior released .deb sizes for reference: 0.0.4 = 5.4 KB, 0.0.5 = 6.28 KB.

## STANDING WORKFLOW RULE (apply automatically every release cycle)
- After each release cycle, RUN A SCRATCH CLEANUP of ~ and ~/ftp:
  remove abandoned attempts, superseded scripts, chunk leftovers (m.b64/lp.py/etc.),
  early transfer copies; thin repo .bak files to newest 1.
  ALWAYS: archive removed files first (reversible); KEEP the release .deb,
  *.usrlocal-backup-*, and any files still needed for a pending push/release
  (detect via: is vX.Y.Z tag pushed to origin?). Provide as a paste-safe/viewer script.
- Also proactively OFFER cleanup at the end of each release, don't wait to be asked.

## PROOT/USERLAND TAR RULE (critical for all scripts)
- UserLAnd proot CANNOT stat absolute paths in tar -> "Cannot stat: Function not implemented".
- ALWAYS archive with:  tar -czf ARCHIVE -C "$HOME" <relative paths>   (never absolute).
- Same reason the .deb must be built with tar --owner=0 --group=0 (+ -C). Never feed tar abs paths.

## PROOT TAR -c IS FULLY BROKEN on this device (updated finding)
- Even `tar -c` on a SINGLE file with relative path fails: "Cannot stat: Function not implemented".
- => Cannot create tarballs on-device at all. For reversibility use a TRASH FOLDER
  (mv into ~/.051n7_trash/<ts>/) instead of archiving. Restore by mv back.
- NOTE: `tar -x` (extract) works (used it for viewer files); only `tar -c` (create) is broken.
  The .deb build's data.tar.gz is created by... need to recheck: clean_release_006.sh used
  `tar --owner=0 --group=0 -C ...` and the .deb built fine (8998 bytes). So tar -c with -C
  MAY work when CWD-relative; the failing cases used $HOME-relative from a different cwd.
  For cleanup, TRASH-FOLDER approach is the safe, guaranteed-working choice.
