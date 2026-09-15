# Changelog

All notable changes to tunshare are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- LAN `:53` resolver. Clients always query this Mac. Blocklist NXDOMAINs ads. WAN bypass sends matching names out the WAN, not the VPN. Both stay off until you toggle them.
- Per-source Domain filters. Custom URLs add and remove in the TUI. Extra builtins default off.
- Optional names on custom list sources.
- Connection card shows Block, WAN bypass, and a session blocked-query count.
- `tunshare status` live inspect, with `--check NAME`.

### Changed
- The LAN MTU knob is now Tunnel MTU. Auto probes the real path MTU for the pf MSS clamp. A fixed value uses the number you set.

### Fixed
- MSS clamp matches inbound LAN SYNs on the share iface, before source NAT. Fat TLS through encapsulating tunnels no longer RST.
- Empty Hickory answers map to NODATA/NXDOMAIN, not SERVFAIL.
- Apple `scrub-anchor` stays before tunshare NAT hooks, so MAIN `-f` does not die at line 10.
- Sharing lives in `com.tunshare`. Health re-merges the six MAIN hooks and heals lost `route-to` without flushing the persist table.
- Bypass lookups ask the WAN gateway on BoundIf sockets.
- WAN ranking uses table defaults and fails closed on empty ARP. Hairpin share-LAN uplinks are skipped.
- Geosite TLD tokens like `domain:ir` match the whole suffix.
- WAN DNS sockets pin to the uplink with `IP_BOUND_IF`.
- Apple pf `route-to` sits before `from`, so WAN bypass loads.
- MSS clamp uses probed path MTU, not the tunnel's interface MTU.

## [0.3.0] - 2026-05-27

## [0.2.0] - 2026-05-27

### Added
- `--version` and `--help` flags.
- Homebrew distribution via `kumamaki/homebrew-tap`.
- App state-machine smoke tests.
- **VPN-drop auto-recovery.** When the VPN interface drops mid-session,
  tunshare now reacts according to a configurable `vpn_drop_strategy`:
  `WaitWithTimeout(15s)` (default), `AutoStop`, or `Ignore`. Header shows
  a live countdown while waiting.
- **DNS history.** The DNS picker remembers up to 10 recently-used
  custom DNS servers under the built-in presets; press `[x]` to remove
  one. Old single-slot `custom_dns` configs auto-populate history on
  first load.
- **Doctor.** New "Run Doctor" menu entry and `--doctor` CLI flag run
  12 diagnostic checks (privilege, required tools, dnsmasq, IP
  forwarding state, pf enabled, stale anchor, macOS Internet Sharing
  conflict, foreign dnsmasq, NAT-PMP port, VPN interface, LAN
  interface, config dir writable). In-app: `[c]` flushes a stale
  `vpn_share` pf anchor inline; `[r]` re-runs. CLI exits non-zero on
  any fail for scripting.

### Changed
- Internal `system::run_cmd` helper consolidates the shell-out + error
  conversion pattern previously repeated across every `system/` module.
- DNS picker code paths restructured to remove unwrap() calls.

### Removed
- Dead code: `App::is_loading`, `IpForwarding::disable`, `Firewall::is_loaded`,
  and unused `NoVpnInterfaces` / `NoLanInterfaces` error variants.

## [0.1.0] - 2025-11-08

### Added
- Initial public release.
- NAT via macOS `pf` firewall.
- VPN/LAN interface auto-detection with manual override.
- Optional DHCP server via `dnsmasq`.
- Native NAT-PMP server (RFC 6886) replacing external `miniupnpd`.
- DNS picker with Cloudflare/Google/Quad9 presets and custom override.
- Persistent preferences (`~/.config/tunshare/config.json`).
- Periodic VPN/IP-forwarding health monitoring shown in the header.
- Debug overlay with live pf rules, NAT-PMP mappings, interface state.
- Drop-safe cleanup of all firewall, IP forwarding, DHCP, and NAT-PMP state.

[Unreleased]: https://github.com/kumamaki/tunshare/compare/v0.3.0...HEAD
[0.3.0]: https://github.com/kumamaki/tunshare/releases/tag/v0.3.0
[0.2.0]: https://github.com/kumamaki/tunshare/releases/tag/v0.2.0
[0.1.0]: https://github.com/kumamaki/tunshare/releases/tag/v0.1.0
