# DNS Record Management — Vendor Index

One row per supported vendor. Each vendor directory holds its own `procedure.md` (the full step-by-step),
plus wire-level `endpoints.md` and, where the vendor has non-obvious per-type field rules, `record-fields.md`.

| Vendor / Platform | Directory | Scope | Notes |
|---|---|---|---|
| AWS Route 53 | `route53/` | Create/update(upsert)/delete a basic record, with propagation confirmation | Change-batch model, `PENDING`→`INSYNC` polling |
| Azure DNS | `azure/` | Create/update/delete a record **set** (not a single record), add/remove within a set | Record-set (not record) model; zone-apex SOA/NS restrictions |
| Cloudflare | `cloudflare/` | Create a record (create-only) | Proxy-status gate requires operator confirmation |
| Infoblox NIOS | `infoblox/` | Create/read/update/delete A/AAAA/CNAME/MX/TXT/PTR | `_ref`-addressed object model (WAPI); grounded in Infoblox's own official WAPI reference guide, with per-type field names cross-checked against Infoblox's generated WAPI object schema — see `procedure.md` for the one unconfirmed detail (exact duplicate-object error string) |
| BlueCat Horizon / DDI Orchestrator | `bluecat/` | Create a record (create-only) | Record type is immutable once created; field names drawn from a secondary source — verify against the target instance |
| PowerDNS Authoritative Server | `powerdns/` | Create/replace/delete an RRset, plus incremental single-record add (`EXTEND`)/remove (`PRUNE`) without touching the rest of the RRset | The only platform here with a native incremental-add/-remove primitive — others require a read-modify-write cycle for the same "add one value to a multi-value record" ask. Also the only platform with in-scope DNSSEC rectification (see note below). |

**Not in scope for this skill:** NetBox IPAM (prefix/IP address allocation is a different capability —
see the separate `ipam_allocation` skill, not a DNS vendor variant).

**Universal-concepts correction (found adding PowerDNS):** `SKILL.md`'s "Known limitations" originally
said DNSSEC record types are out of scope on every platform "that offers them" — PowerDNS's zone
rectification (needed after any RRset change on a DNSSEC-enabled zone) is genuinely in scope for this
skill, since it's a required follow-up step to a record change, not a DNSSEC *record type* (DS/DNSKEY)
being created directly. `SKILL.md` was corrected to distinguish "creating DNSSEC record types
directly" (still out of scope everywhere) from "DNSSEC-related post-change housekeeping a platform
requires" (in scope where a platform's own record-change procedure requires it).

## Adding a new vendor

1. Create `references/<vendor>/procedure.md` following the shape of an existing vendor file (Operational
   procedure → Patterns → Known limitations → Tools → Verification checklist).
2. Add `endpoints.md` (wire-level calls) and `record-fields.md` (if the vendor has per-type field rules
   worth a dedicated table) alongside it.
3. Add one row to the table above.
4. Do NOT add vendor-specific procedure content to the top-level `SKILL.md` — see that file's own
   contribution note.
