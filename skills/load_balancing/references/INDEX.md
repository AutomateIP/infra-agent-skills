# Load Balancing — Vendor Index

One row per supported vendor. Each vendor directory holds its own `procedure.md` (the full step-by-step),
plus wire-level `endpoints.md`.

| Vendor / Platform | Directory | Scope | Notes |
|---|---|---|---|
| F5 BIG-IP LTM | `f5_big-ip_ltm/` | Object-level iControl REST CRUD for `ltm virtual` (VIP) and `ltm pool`/pool members. | Grounded in F5 clouddocs' iControl REST API reference, which carries F5's own notice that it is community-contributed content not monitored by F5 — see the procedure's sourcing caveat. Does not cover AS3 declarative onboarding. Auth mechanism not independently confirmed. |
| Citrix ADC (NetScaler) | `citrix_adc/` | NITRO REST CRUD for `lbvserver` (VIP), `service` (pool-member equivalent), and `lbvserver_service_binding`. | Grounded in NetScaler developer docs (current-release/14.1 tree). Does not cover the `servicegroup` object/binding variant — flagged as an unconfirmed gap, not covered. Two documented auth mechanisms not fully reconciled with each other in the source — see `endpoints.md`. |
| HAProxy | `haproxy/` | Config-file `frontend`/`backend`/`server` block definition, plus Runtime API (stats socket) commands for live server state (drain/maintenance/weight) without reload. | CLI/config-driven — no `endpoints.md` (see `AGENTS.md` rule 6). Does not cover HAProxy's separate REST-based Data Plane API — its docs are a JS-rendered page that couldn't be retrieved as content in this research pass; flagged as a known gap for a future contribution. |

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
