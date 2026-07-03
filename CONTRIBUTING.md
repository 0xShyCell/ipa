# Contributing to ipa

`ipa` is a single-purpose bash script. It has no dependencies beyond `bash`
and `ip`, and no test suite — review is manual, so please keep PRs small and
focused on one change.

Before opening a PR, run `bash -n ipa` to confirm the script still parses,
and paste real terminal output (not a description) in the PR showing the
change working.

## Contribution scope

### Safe to merge

Changes here are low-risk and won't affect classification results or output
correctness for existing users:

- Typo/wording fixes in the help text or README
- Cosmetic table changes that don't affect column logic (box-drawing
  character swaps, color choices, spacing)
- Documentation improvements (README, examples, this file)
- Adding new, clearly-scoped CLI flags that don't change default behavior
  (e.g. a `--no-color` flag)
- Additional shell/OS compatibility notes, if verified by the contributor

### Discuss first (open an issue before a PR)

These touch the parts of the script that determine what a user is told is
their VPN IP vs. LAN IP vs. something else — getting this wrong during a
live engagement is the actual failure mode this tool exists to prevent, so
changes here need discussion before code:

- Changes to `VPN_KEYWORDS` (interface-name VPN detection regex)
- Changes to `is_private_ipv4` or the IPv6 ULA prefix check (LAN detection)
- Changes to default behavior (what `ipa` does with zero arguments)
- Changes to how the "no VPN found" placeholder row behaves
- Any new output format (JSON, CSV, etc.) — needs agreement on schema before
  implementation
- Adding a new dependency beyond `bash`/`ip`/`tput`

### Out of scope

Not going to be merged, to keep this a small, auditable, dependency-free
script:

- Any feature that makes outbound network calls (e.g. "detect my public IP"
  via an external service) — this tool only reads local interface state
- Windows-native support (WSL running Linux `ip` is a different story and
  falls under "discuss first" if someone verifies it)
- A GUI or TUI beyond the current table output
- Telemetry, analytics, or update-checking of any kind
- Rewriting the tool in another language

## Reporting issues

Include your OS, `bash --version`, and whether `ip` is native or via
`iproute2mac`, plus the exact command and actual vs. expected output.
