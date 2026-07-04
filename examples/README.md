# Examples

Real terminal output captured during lab work, IPs redacted where public/host
IPs. Not staged for this repo — these are actual sessions.

| File              | Command     | What it shows                                                       |
| ----------------- | ----------- | ------------------------------------------------------------------- |
| `ipa-default.txt` | `ipa`       | Default IPv4 view, all interfaces                                   |
| `ipa-4.txt`       | `ipa 4`     | Explicit IPv4-only (identical to default)                           |
| `ipa-6.txt`       | `ipa 6`     | IPv6 view — no IPv6 VPN address present, placeholder row shown      |
| `ipa-both.txt`    | `ipa both`  | Combined IPv4 + IPv6 columns in one table                           |
| `ipa-4-vpn.txt`   | `ipa 4 vpn` | Filtered to VPN-type interfaces only                                |
| `ipa-tun0.txt`    | `ipa tun0`  | Single-argument shorthand, filtered to the `tun0` interface by name |
| `ipa-vpn.txt`     | `ipa vpn`   | Filtered to VPN type, default IPv4                                  |

## Before / after: finding your VPN IP mid-engagement

**Before**, the manual equivalent of what these commands replace:

```
$ ip -4 addr show tun0
# or, if you don't remember the interface name:
$ ip a | grep -A2 tun
```

Both require knowing the interface name up front, or reading through
`inet`/`inet6` lines mixed in with flags, MTU, and scope info to find the one
number you want.

**After** — three equivalent ways to get the same answer, all one line, none
requiring you to already know the interface is called `tun0`:

```
$ ipa vpn
┌──────┬───────────┬─────────────────┐
│ Type │ Interface │    IPv4 Address │
├──────┼───────────┼─────────────────┤
│ VPN  │   tun0    │ 192.168.161.234 │
└──────┴───────────┴─────────────────┘
```

`ipa tun0` and `ipa 4 vpn` produce the identical row (see `ipa-tun0.txt`
and `ipa-4-vpn.txt`) — you can filter by type when you don't remember the
interface name, or by name when you do.

This is the real workflow this tool was built for: connect to a lab/OSCP/HTB
VPN, run `ipa vpn`, get the tunnel IP without parsing anything by eye.
