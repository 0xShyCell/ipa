# Changelog

All notable changes to this project are documented here.
Format loosely based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [1.0.0] — 2026-07-04

### Added

- Initial public release
- IPv4 display (default) via `ip -4 addr show`
- IPv6 display via `ip -6 addr show` (`ipa 6`)
- Combined IPv4 + IPv6 display (`ipa both`)
- Interface classification into `LAN`, `VPN`, or `Other`
  - VPN: interface name matches `tun|tap|vpn|wg|ppp|ipsec|vti|gre|gretap|erspan`
  - LAN (IPv4): `10.0.0.0/8`, `192.168.0.0/16`, `172.16.0.0/12`
  - LAN (IPv6): ULA range (`fc00::/7`)
- Filtering by type (`all`, `vpn`, `lan`) or by exact interface name
- Shorthand single-argument form for interface filtering (`ipa tun0` ==
  `ipa 4 tun0`)
- Adaptive box-drawing table that resizes columns to actual content width
- Color output via `tput`, with automatic graceful degradation when not
  attached to a TTY or when `tput` is unavailable
- `-h` / `--help` flag
- Loopback (`127.0.0.1`, `::1`) and IPv6 link-local (`fe80::`) addresses
  excluded from output
- Placeholder `VPN` row with `--` values shown when no VPN interface is
  detected and the active filter would otherwise include VPN rows
