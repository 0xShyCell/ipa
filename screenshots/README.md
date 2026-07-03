# Screenshots checklist

What to capture, the exact command, and what it's meant to visually prove.
"Have" = you've already captured this one. "Capture" = worth adding.

| # | Status | Command | Proves |
|---|---|---|---|
| 1 | Have | `ipa` | Baseline: clean default table, LAN interfaces + Docker bridges correctly classified, VPN placeholder row when no VPN connected |
| 2 | Have | `ipa` (in terminal app chrome, not bare shell) | Same as #1, but shows it in context — useful as the README hero image |
| 3 | Have | `ipa 6` then `ipa both` back to back | Column layout adapting: IPv6-only mode collapses to placeholder when no IPv6 VPN present, `both` mode adds a column without breaking table alignment |
| 4 | Have | `ipa 6` / `ipa both` on macOS with real IPv6 addresses present | Cross-platform: same script, same output shape, on macOS via `iproute2mac`, with populated IPv6 `Other` rows (public/global addresses correctly *not* misclassified as LAN) |
| 5 | Capture | `ipa vpn` or `ipa tun0` while connected to a lab/OSCP VPN | The actual killer feature: VPN interface correctly detected and isolated by name, with a real `tun0` IP — this is the strongest portfolio image since it demonstrates the classification logic working, not just the table renderer |
| 6 | Capture | `ipa --help` | Documents the full flag/argument surface in one image, useful for anyone skimming the repo without reading the README |
| 7 | Capture | `ipa nonexistent0` (a filter matching no interface) | Shows the "no interfaces matched filter" fallback message — proves error handling was considered, not just the happy path |
| 8 | Capture (optional) | Wide terminal vs. narrow terminal, same command | Demonstrates the adaptive column widths claim in the README rather than just asserting it |
