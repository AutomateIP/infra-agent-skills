# DNS Zone Management — Vendor Index

One row per supported vendor. Each vendor directory holds its own `procedure.md` (the full step-by-step),
plus wire-level `endpoints.md`.

| Vendor / Platform | Directory | Scope | Notes |
|---|---|---|---|
| AWS Route 53 | `route53/` | Create/read/update(comment)/delete a hosted zone; SOA/NS defaults; subdomain delegation from an externally-hosted parent domain | Also in `dns_record_management`'s INDEX.md (record-level ops there, zone lifecycle here — separate procedures, not merged). Deletion requires the zone reduced to only its default SOA/NS records first, and is documented as irreversible. |
| Cloudflare | `cloudflare/` | Create/read/delete a zone; setup-type selection (full/partial/secondary/internal); full-setup activation flow | Also in `dns_record_management`'s INDEX.md. Some detail sourced via an LLM-mediated documentation fetch rather than a raw-page read — see that vendor's `procedure.md` Known limitations before treating as exhaustive (exact error list, subdomain-setup compatibility matrix unconfirmed). No documented zone-must-be-empty precondition for delete was found (contrast with Route 53) — treat as unconfirmed, not as "no such constraint." |
| PowerDNS Authoritative Server | `powerdns/` | Create/read/update/delete a zone; `kind`/`nameservers`/replication-adjacent config; where subdomain delegation fits | Also in `dns_record_management`'s INDEX.md (RRset-level ops there, zone lifecycle here). Some detail sourced via an LLM-mediated documentation fetch — full `kind` enum, exact default SOA content, and exact error strings are flagged unconfirmed in that vendor's `procedure.md`. |

**Not yet covered:** Azure DNS and BlueCat both appear in `dns_record_management`'s vendor set but were
not added here in this pass — Azure DNS's zone-level REST API (`Zones - Create Or Update`) was spot-
checked during this research and does look real and well-documented (tags, `zoneType`
Public/Private, `nameServers`/`maxNumberOfRecordSets` in the response), so it's a reasonable next
candidate; it just wasn't carried through to a full procedure/endpoints pair in this pass to keep
depth over breadth. BlueCat and Infoblox NIOS zone-level APIs were not researched at all here.

## Adding a new vendor

1. Create `references/<vendor>/procedure.md` following the shape of an existing single-vendor
   capability (e.g. `skills/cmdb_asset_management/references/servicenow/procedure.md`) as a template:
   Operational procedure → Patterns → Known limitations → Tools → Verification checklist.
2. Add `endpoints.md` (wire-level calls) alongside it, if the platform is REST/API-driven — CLI-driven
   platforms keep commands inline in `procedure.md` instead (see `AGENTS.md` rule 6).
3. Add one row to the table above, replacing or extending the placeholder/existing rows.
4. If this vendor also appears in `dns_record_management`'s `INDEX.md`, note that in this row's Notes
   column — the two skills share vendors often but are separate procedures (zone-level vs.
   record-level), never merge their reference files.
5. Check every existing claim in `SKILL.md`'s "Universal concepts" section against this vendor's actual
   behavior before assuming it holds — correct that section explicitly if it doesn't (see `AGENTS.md`
   rule 4). In particular, this skill's universal concepts currently assume no shared SOA/NS-generation
   behavior across every vendor (see SKILL.md) — verify whether that still holds before adding to it.
6. Do NOT add vendor-specific procedure content to the top-level `SKILL.md` — see that file's own
   contribution note.
