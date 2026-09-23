# DHCP Scope Management — Vendor Index

One row per supported vendor. Each vendor directory holds its own `procedure.md` (the full step-by-step),
plus wire-level `endpoints.md`.

| Vendor / Platform | Directory | Scope | Notes |
|---|---|---|---|
| Infoblox NIOS | `infoblox/` | Create/update/delete a DHCP `range` inside an existing network, exclude specific addresses, create/update/delete a `fixedaddress` client reservation — via WAPI | REST-like WAPI. Also appears in `ipam_allocation`'s `INDEX.md` (`infoblox/` there covers network/IP allocation as inventory only) — the two Infoblox directories are deliberately separate procedures; do not merge them. Sourced from a third-party WAPI-doc mirror (Infoblox's own docs require an authenticated Grid Master session) — re-verify field names against the target Grid Master's WAPI version before automating. |
| ISC Kea | `isc_kea/` | Create/update(full or delta)/delete a DHCPv4 subnet, manage shared networks, create/get/update/delete a host reservation — via the Kea Control Agent's `subnet_cmds`/`host_cmds` hook command API | JSON-over-HTTP command/response envelope (`command`/`service`/`arguments` request; `result`/`text`/`arguments` response) — not literal HTTP method+path semantics the way REST is; see `procedure.md`'s own note that the same result code means different things across different commands. Does not appear in `ipam_allocation` (Kea isn't covered there). DHCPv4-primary; IPv6 command names exist but weren't independently verified. |
| Microsoft DHCP Server | `microsoft_dhcp/` | Create/update/delete an IPv4 scope, create/delete a client reservation — via the `DhcpServer` PowerShell module | **CLI/cmdlet-driven, not REST** — no `endpoints.md`, per `AGENTS.md` rule 6 (CLI syntax stays inline in `procedure.md`). Does not appear in `ipam_allocation` (not covered there). IPv4-only in this draft; the parallel `*Dhcpv6*` cmdlet family wasn't researched. |

There is no single wire standard grounding DHCP scope/lease management across vendors — RFC 2131
defines the DHCP protocol clients and servers speak on the wire, but says nothing about how an
operator's management API/CLI should represent a scope or reservation as a configuration object;
each platform above designed that management surface independently (NIOS's `range`/`fixedaddress`
objects, Kea's `subnet_cmds`/`host_cmds` command API, Windows's `DhcpServer` cmdlet family).
`SKILL.md`'s "Universal concepts" section reflects that: it states only the handful of structural
claims genuinely verified across all three platforms above (see that section's own citation of which
vendor confirmed each claim), not an assumed DHCP-wide standard.

**A genuinely different call mechanism, as in `ipam_allocation`:** Infoblox NIOS is REST-like WAPI,
Kea is a JSON command/response envelope over HTTP (not literal HTTP-method-per-operation semantics),
and Microsoft DHCP Server is PowerShell/CIM cmdlets with no wire-level API surface documented here at
all. `SKILL.md`'s "Tools" section and this table state the mechanism per-platform rather than
assuming REST.

## Adding a new vendor

1. Create `references/<vendor>/procedure.md` following the shape of an existing single-vendor
   capability (e.g. `skills/cmdb_asset_management/references/servicenow/procedure.md`) as a template:
   Operational procedure → Patterns → Known limitations → Tools → Verification checklist.
2. Add `endpoints.md` (wire-level calls) alongside it, if the platform is REST/API-driven — CLI-driven
   platforms keep commands inline in `procedure.md` instead (see `AGENTS.md` rule 6).
3. Add one row to the table above, replacing the placeholder row.
4. If this vendor also appears in `ipam_allocation`'s `INDEX.md`, note that in this row's Notes column
   — the two skills often share vendors but are separate procedures (address-space allocation vs.
   DHCP service configuration), never merge their reference files.
5. Check every existing claim in `SKILL.md`'s "Universal concepts" section against this vendor's actual
   behavior before assuming it holds — correct that section explicitly if it doesn't (see `AGENTS.md`
   rule 4).
6. Do NOT add vendor-specific procedure content to the top-level `SKILL.md` — see that file's own
   contribution note.
