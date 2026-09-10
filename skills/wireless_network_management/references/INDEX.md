# Wireless Network Management — Platform Index

One row per supported wireless-management platform. Each platform directory holds its own
`procedure.md` (capability language, per the REST convention), `endpoints.md` (literal wiring), and
`source-extract.md`.

| Platform | Directory | Scope | Notes |
|---|---|---|---|
| Cisco Meraki | `meraki/` | SSID provisioning (configure a numeric slot 0-14 — no create/delete op exists), update, disable | **No create-SSID endpoint at all** — same PUT call for create/update/disable. Cross-checked against a real, delivered Itential production build (`projects/meraki-ssid-provisioning/`), which live-verified two facts beyond the vendor docs: `8021x-radius` hard-requires a RADIUS server, and the PUT response doesn't always echo `vlanId` even when applied. |
| HPE Aruba Networking Central | `aruba_central/` | WLAN listing, detail, and throughput monitoring — **read-only** | No documented public create/update WLAN/SSID REST endpoint exists (checked both Classic and New Central API generations) — provisioning is only exposed via an SDK-wrapped Python workflow script whose underlying calls aren't published. Deliberately scoped read-only rather than reverse-engineering the SDK. |
| Cisco Catalyst Center | `catalyst_center/` | Create and provision an SSID (fabric or non-fabric, one call), fabric SSID-to-VLAN mapping | Asynchronous — every write returns a task/execution identifier that must be polled to confirm actual completion. The VLAN-mapping call is destructive on omission (an unlisted VLAN's existing mappings are cleared), the opposite of typical partial-update semantics. |

**Cross-vendor asymmetry, by design of this research pass:** Meraki and Catalyst Center both support
provisioning; Aruba currently doesn't (publicly). This isn't a gap in this skill's coverage — it's an
accurate reflection of what each vendor's public API actually exposes today. Re-check Aruba's docs
periodically; if a write endpoint is published later, this is the file to update.

## Adding a new platform

1. Create `references/<platform>/procedure.md` in capability language (no literal HTTP method+path —
   see `endpoints.md` convention), following the shape of `meraki/procedure.md` (Operational
   procedure → Patterns → Known limitations → Tools → Verification checklist).
2. Add `endpoints.md` with the literal wiring for every capability named in `procedure.md`.
3. Add `source-extract.md` for the isolated procedural extract.
4. Add one row to the table above. If the platform's public API doesn't support write operations for
   the relevant capability, say so explicitly rather than silently building a thinner skill — that's a
   real, useful finding in itself (see Aruba).
5. Do NOT add platform-specific procedure content to the top-level `SKILL.md` — see that file's own
   contribution note.
