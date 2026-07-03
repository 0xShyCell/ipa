# ipa — Network IP Address Formatter

A small bash tool that replaces `ip a` / `ifconfig` / `ip -4 addr show` with a clean,
classified, adaptive table of your interfaces and IPs. Built for quick "what's my
VPN IP / LAN IP right now" checks during lab and CTF work.

```
$ ipa
┌──────┬─────────────────┬─────────────────┐
│ Type │    Interface    │    IPv4 Address │
├──────┼─────────────────┼─────────────────┤
│ LAN  │      eth0       │   10.10.236.240 │
│ LAN  │ br-44d3053b2e30 │      172.18.0.1 │
│ LAN  │     docker0     │      172.17.0.1 │
│ VPN  │      tun0       │ 192.168.161.234 │
└──────┴─────────────────┴─────────────────┘
```

## Why

`ip a` and `ifconfig` dump every interface with full flags, MTU, MAC address, and
scope info, all in a format meant for completeness, not for scanning quickly.
On a machine with Docker bridges, a VPN tunnel, and a couple of physical/virtual
NICs, finding "which line is my `tun0` IP" means reading past a lot of noise you
don't need in that moment — which comes up constantly in OSCP/HTB/CTF workflows
where you need your VPN IP to hand to a payload or a listener.

`ipa` does three things `ip a` doesn't:

- **Classifies** each interface as `LAN`, `VPN`, or `Other` (VPN detection is
  interface-name based — see [How it classifies interfaces](#how-it-classifies-interfaces))
- **Filters** in the same command you'd otherwise pipe into `grep`/`awk` —
  `ipa vpn`, `ipa tun0`, `ipa 4 lan`
- **Renders as a table** that resizes to the actual content instead of a fixed
  width, and stays readable whether you're printing one interface or all of them

It's not a replacement for `ip a` when you need full interface detail (MTU,
flags, MAC, scope) — it's a faster path to the one or two numbers you actually
need mid-engagement.

## Installation

### Linux

```bash
curl -o ipa https://raw.githubusercontent.com/0xShyCell/ipa/main/ipa
chmod +x ipa
sudo mv ipa /usr/local/bin/ipa
```

`ip` (iproute2) ships by default on essentially every modern distro — no extra
step needed.

### macOS

macOS has no native `ip` command, so install `iproute2mac` first:

```bash
brew install iproute2mac
curl -o ipa https://raw.githubusercontent.com/0xShyCell/ipa/main/ipa
chmod +x ipa
sudo mv ipa /usr/local/bin/ipa
```

`iproute2mac` aliases `ip` to `ifconfig`/`netstat` output in iproute2-compatible
syntax, which is what `ipa` actually parses — without it, `ipa` will run but
report no interfaces.

---

Either way, once installed you can also just clone the repo and symlink/copy
the `ipa` file into any directory on your `PATH`.

## Usage

```
ipa – Network IP Address Formatter

USAGE
  ipa [version] [filter]

VERSION  (optional, default: 4)
  4          IPv4 addresses only
  6          IPv6 addresses only
  both       IPv4 and IPv6

FILTER  (optional, default: all)
  all        All interfaces
  vpn        VPN interfaces only  (tun, tap, wg, ppp, …)
  lan        LAN interfaces only
  <iface>    Specific interface by name  (e.g. eth0, en0, tun0)

EXAMPLES
  ipa                  IPv4, all interfaces
  ipa 6                IPv6, all interfaces
  ipa both             IPv4 + IPv6, all interfaces
  ipa 4 vpn            IPv4, VPN interfaces only
  ipa 4 lan            IPv4, LAN interfaces only
  ipa both eth0        IPv4 + IPv6, eth0 only
  ipa tun0             Shorthand: IPv4, tun0 only

FLAGS
  -h, --help           Show this help message
```

This is the actual `-h` output — not a paraphrase.

## Real examples

Captured from an active session, VPN IP redacted:

```
$ ipa vpn
┌──────┬───────────┬─────────────────┐
│ Type │ Interface │    IPv4 Address │
├──────┼───────────┼─────────────────┤
│ VPN  │   tun0    │ 192.168.161.234 │
└──────┴───────────┴─────────────────┘
```

`ipa tun0`, `ipa vpn`, and `ipa 4 vpn` all resolve to the same row here — pick
whichever is faster to type. More real captures, across every mode, are in
[`examples/`](examples/).

## How it classifies interfaces

- **VPN**: interface name matches `tun|tap|vpn|wg|ppp|ipsec|vti|gre|gretap|erspan`
  (case-sensitive substring match against the interface name reported by `ip`)
- **LAN** (IPv4): address falls in `10.0.0.0/8`, `192.168.0.0/16`, or
  `172.16.0.0/12`
- **LAN** (IPv6): address starts with `fc` or `fd` (ULA range, `fc00::/7`)
- **Other**: everything else — public IPs, non-standard private ranges, etc.
- Loopback (`127.0.0.1`, `::1`) and link-local IPv6 (`fe80::`) are always
  excluded

Classification is name-based for VPN detection, not based on routing table or
`wg show` / `openvpn` process inspection — a physical NIC you happen to name
`tun-lab` would be flagged VPN, and a VPN client using a non-matching interface
name wouldn't be. This is a known tradeoff, not a bug; see `CONTRIBUTING.md` if
you want to improve it.

## Dependencies

- `bash` (uses `[[ ]]`, regex matching, arrays — will not run under `sh`/`dash`)
- `ip` command (iproute2) — native on Linux, requires
  [`iproute2mac`](https://github.com/brona/iproute2mac) on macOS (see
  [Installation](#installation))
- `tput` — optional, used for color output. If missing or output isn't a TTY,
  colors are silently skipped and the table still renders

## Confirmed working on

- Kali Linux (aarch64, VMware Fusion, Apple Silicon host)
- macOS (Apple Silicon, with `iproute2mac` installed)

Not tested on other distros or on Windows/WSL — should work anywhere `bash` +
`ip` (or `iproute2mac`) are available, but that's untested, not confirmed.

## Known limitations

- VPN detection is interface-name pattern matching only (see above)
- No JSON/machine-readable output mode yet — table only
- No IPv6 LAN detection for interfaces that only report a global/ULA address
  under an unrecognized prefix — these fall into `Other`

## License

MIT — see [LICENSE](LICENSE).
