# Changelog

All notable changes to the 051N7-MAIL project will be documented in this file.

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
