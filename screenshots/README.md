# Screenshots

## Kali Linux

### Default view

**Command**

```bash
ipa
```

![Default view on Kali](ipa-default-kali-.png)

Baseline output: LAN interfaces and Docker bridges correctly classified, VPN row shown as a placeholder since no VPN was connected in this capture.

---

### IPv6 and combined view

**Command**

```bash
ipa 6
```

![IPv6 mode on Kali](ipa-ipv6-kali.png)

```bash
ipa both
```

![Both mode on Kali](ipa-both-kali.png)

Shows the column layout adapting: `ipa 6` alone collapses to a placeholder row when no IPv6 address is present anywhere, while `ipa both` adds the IPv6 column without breaking table alignment.

---

## macOS

### Default view

**Command**

```bash
ipa
```

### IPv6 and combined view

**Command**

```bash
ipa 6
ipa both
```

![IPv6 mode on macOS](ipa-ipv6-macOS.png)

![Both mode on macOS](ipa-both-macOS.png)

Same script, same output shape, running on macOS via `iproute2mac` — with real global IPv6 addresses present on `en0`, correctly classified as `Other` (not misclassified as `LAN`), confirming the ULA-prefix check doesn't false-positive on public IPv6 ranges.
