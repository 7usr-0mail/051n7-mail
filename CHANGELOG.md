# Changelog

All notable changes to the 051N7-MAIL project will be documented in this file.

## [v0.0.6] - 2026-07-24

### Security
- **Contact files are no longer executed.** Replaced all `source` calls on contact
  files with a safe `KEY=VALUE` parser (`_051n7_read_contact`), closing an arbitrary
  code-execution vector where a crafted contact file could run shell commands.
- **Strict FROM validation.** A sender address is now accepted only when it is a bare
  handle (defaulted to `@051n7.usr`) or ends in exactly `@051n7.usr` / `@051n7.grp`.
  Reserved handles `0mail` and `peek`, the `.dev` domain, and any foreign domain
  (e.g. `name@gmail.com`) are rejected.

### Fixed
- **Multicast `To:` header.** Each recipient's envelope now shows that recipient in the
  `To:` field instead of always the first recipient of the group.
- **Reserved-handle FROM check** is now reachable (previously dead code after a `continue`).
- **Recipient/IP pairing** kept 1:1 when composing to multiple recipients.
- Numerous shell robustness fixes; the script is now ShellCheck-clean.

### Notes
- Known spool/queue limitations are tracked for v0.0.7 (retry worker lifetime,
  manual queue flush, delivery visibility).

## [v0.0.5] - 2026-07-23

### Added
- Folder-based contact directory architecture (`~/.051n7_mail/contacts/`).
- Multicast group handle support (`.grp` folders containing member `.usr` files).
- Automatic handle suffix resolution (`@051n7.usr` / `@051n7.grp`).
- Case-insensitive composer navigation (`PREV`, `CANCEL`, `CONTACTS`, `QUEUE`).
- Outbound spool auto-retry queue worker.
- Clean TUI form redraw engine for mobile terminal compatibility.

## [v0.0.1] - Initial Release
- Basic GPG encryption over raw Yggdrasil IPv6 sockets.
