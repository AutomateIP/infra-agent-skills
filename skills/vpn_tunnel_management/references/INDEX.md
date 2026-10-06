# VPN Tunnel Management — Vendor Index

One row per supported vendor/platform. Each vendor directory holds its own `procedure.md` (the full
step-by-step) plus a companion field-reference file (`endpoints.md` for REST/API-driven platforms, or
a descriptively-named field-reference file for CLI-driven platforms per `AGENTS.md` rule 8).

| Vendor / Platform | Directory | Scope | Notes |
|---|---|---|---|
| strongSwan | `strongswan/` | Site-to-site (and host-to-host) IPsec/IKE tunnel via `swanctl`/`swanctl.conf`, current control interface | CLI/config-driven, no `endpoints.md`. Does not cover the legacy `ipsec.conf`/`starter`/`stroke` toolchain, EAP/XAuth remote-access profiles, or PKI/certificate provisioning. |
| Palo Alto Networks | `paloalto_panos/` | Route-based (and policy-based Proxy-ID) site-to-site IPsec VPN: IKE Gateway, IKE/IPSec Crypto Profiles, IPsec Tunnel object, tunnel interface | `endpoints.md` documents field names/option lists; procedure covers web UI/XML API/CLI generically. Does not cover GlobalProtect remote access, certificate/PKI lifecycle, or security-policy/NAT configuration. |
| Juniper SRX (Junos OS) | `juniper_srx/` | Route-based, autokey-IKE site-to-site IPsec VPN: IKE/IPsec proposal, policy, gateway, VPN object, `st0` tunnel interface | CLI-driven; companion file is `ike-ipsec-proposal-fields.md` (not a bare `endpoints.md` — see naming note in that file). Does not cover policy-based VPN, remote-access/dial-up VPN, Auto Discovery VPN, or hub-and-spoke NHTB. |
| Cisco ASA | `cisco_asa/` | Route-based IPsec VPN via static or dynamic Virtual Tunnel Interface (VTI): IPsec proposal/transform set, IPsec profile, tunnel interface or virtual template + tunnel-group attachment. **Create/configure only** — the source documents no read, update, or delete | CLI-driven; `endpoints.md` holds the literal ASA commands and `procedure.md` refers to them by operation name (differs from Juniper/strongSwan, which keep commands inline; bare `endpoints.md` name kept at the contributor's request — rename to a `vti-`-prefixed pair if a second ASA mode, e.g. crypto-map VPN, is added, per `AGENTS.md` rule 8). The source gives no verification commands. Does not cover policy-based (crypto map) VPN, remote access, IP SLA tracking, or the peer's configuration. |

## Adding a new vendor

1. Create `references/<vendor>/procedure.md` following the shape of an existing vendor in this skill
   (e.g. `strongswan/procedure.md` for a CLI-driven platform, `paloalto_panos/procedure.md` for one
   with both GUI/API and CLI surfaces) as a template: Operational procedure → Verification → Known
   limitations → Tools → Verification checklist.
2. Add a companion field-reference file alongside it — `endpoints.md` for a REST/API-driven platform,
   or a descriptively-named file (e.g. `<platform-family>-fields.md`) for a CLI-driven one, per
   `AGENTS.md` rule 8. Do not use a bare `endpoints.md` name once a vendor might plausibly gain a
   second platform-family file later — name it descriptively from the first file.
3. Add one row to the table above.
4. Check every existing claim in `SKILL.md`'s "Universal concepts" section against this vendor's
   actual behavior before assuming it holds — correct that section explicitly, and say so in the PR
   description, if it doesn't (see `AGENTS.md` rule 4). The current claims (phase1/phase2 vs. IKE_SA/
   CHILD_SA terminology equivalence, config-success vs. established-tunnel distinction, and universal
   confirmation-gating on tunnel/SA teardown) were verified across strongSwan, PAN-OS, and Juniper SRX
   — a fourth vendor could still contradict one of them.
5. Do NOT add vendor-specific procedure content to the top-level `SKILL.md` — see that file's own
   contribution note.

## Research notes (not vendor-specific corrections, but relevant to future additions)

- Cisco's own documentation domain (`cisco.com`, both the `/td/docs/` and `/support/docs/` paths)
  was not reachable via either direct HTTP fetch or a browser-emulating fetch tool during this
  skill's research pass (specific documentation pages consistently returned 404/403 while the general
  site loaded normally) — Cisco IOS-XE/ASA was originally considered for this skill but dropped in
  favor of Juniper SRX, whose documentation was genuinely and consistently fetchable. A future
  contributor attempting Cisco should first confirm current doc-site accessibility rather than
  assuming the same blockage persists.
- Palo Alto's and Juniper's real current documentation paths were only discoverable via each site's
  own `sitemap.xml` (Palo Alto: `docs.paloaltonetworks.com/sitemap.xml`; Juniper:
  `www.juniper.net/documentation/sitemap/sitemap.xml`, itself a sitemap-index pointing to per-locale
  sub-sitemaps) — guessed URL slugs based on older or other vendors' doc conventions consistently
  404'd for both. Checking the sitemap first is a more reliable path than guessing slugs when a
  vendor's docs use a JS-rendered table of contents that a fetch tool can't traverse directly.
