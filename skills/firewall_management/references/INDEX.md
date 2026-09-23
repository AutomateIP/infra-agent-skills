# Firewall Management — Vendor Index

One row per supported vendor. Each vendor directory holds its own `procedure.md` (the full step-by-step),
plus wire-level `endpoints.md`.

| Vendor / Platform | Directory | Scope | Notes |
|---|---|---|---|
| Palo Alto Networks PAN-OS / Panorama | `panos/` | Address-object CRUD, security-policy-rule CRUD (incl. Panorama pre-rulebase), and the commit step, via the PAN-OS REST API | Candidate-vs-running config model: every write lands in the candidate config until a commit is issued. NAT-rule REST path and the commit request's exact literal fields were not confirmed this pass — see `procedure.md` Known limitations. Commit itself requires the older XML API, not the REST API, per the vendor's own documented scope limit on the REST surface. |
| Fortinet FortiOS (FortiGate) | `fortios/` | Address-object, firewall-policy, and destination-NAT (VIP) CRUD via the FortiOS CLI | CLI-driven, not REST-driven — FortiOS's dedicated REST/CMDB API reference is gated behind a Fortinet developer-network login and wasn't independently verified from a public source this pass. Source NAT and address/VIP groups are out of scope this pass. |
| Cisco ASA | `cisco_asa/` | Network-object, extended-ACL (access-list + access-group), and static/auto-NAT CRUD, primarily via the ASA CLI | Targets ASA specifically, not FTD/FMC — FMC's REST API documentation wasn't reachable from a public source this pass and needs its own future research pass/reference file. ASA's own REST API was only confirmed for auth basics and one object example; see `endpoints.md`'s explicit "not confirmed" list before assuming a REST path exists for access-list or NAT operations. |

## Adding a new vendor

1. Create `references/<vendor>/procedure.md` following the shape of an existing single-vendor capability
   (e.g. `skills/cmdb_asset_management/references/servicenow/procedure.md`) as a template: Operational
   procedure → Patterns → Known limitations → Tools → Verification checklist.
2. Add `endpoints.md` (wire-level calls) alongside it, if the platform is REST/API-driven — CLI-driven
   platforms keep commands inline in `procedure.md` instead (see `AGENTS.md` rule 6).
3. Add one row to the table above, replacing the placeholder row.
4. Check every existing claim in `SKILL.md`'s "Universal concepts" section against this vendor's actual
   behavior before assuming it holds — correct that section explicitly if it doesn't (see `AGENTS.md`
   rule 4).
5. Do NOT add vendor-specific procedure content to the top-level `SKILL.md` — see that file's own
   contribution note.
