# Certificate Management — Vendor/Protocol Index

One row per supported platform or protocol. Each directory holds its own `procedure.md` (the full
step-by-step), plus wire-level `endpoints.md`.

| Platform / Protocol | Directory | Scope | Notes |
|---|---|---|---|
| ACME protocol (RFC 8555) — e.g. Let's Encrypt | `acme/` | Issue, renew (as reissue), read, revoke, against any RFC 8555-compliant CA | CA-agnostic protocol coverage; no CA-specific rate limits/policy captured — confirm those against the specific CA in use. |
| HashiCorp Vault — PKI secrets engine | `vault_pki/` | Issue (Vault-generated key), sign (caller CSR), read, revoke, against an already-configured mount/role | Does not cover engine enablement, CA generation, or role configuration. No dedicated renew call — renewal is a fresh issue/sign. |
| AWS Certificate Manager (ACM) | `aws_acm/` | Request (issue), read, renew (managed + explicit), import, delete | Distinct from AWS Private CA's own direct issuance API. Two different renewal mechanisms with different eligibility — see `procedure.md`. |

## Adding a new platform or protocol

1. Create `references/<platform>/procedure.md` following the shape of an existing single-vendor
   capability (e.g. `skills/cmdb_asset_management/references/servicenow/procedure.md`) as a template:
   Operational procedure → Patterns → Known limitations → Tools → Verification checklist.
2. Add `endpoints.md` (wire-level calls) alongside it, if the platform/protocol is REST/API-driven —
   CLI-driven platforms keep commands inline in `procedure.md` instead (see `AGENTS.md` rule 6).
3. Add one row to the table above, replacing the placeholder row.
4. Check every existing claim in `SKILL.md`'s "Universal concepts" section against this platform's
   actual behavior before assuming it holds — correct that section explicitly if it doesn't (see
   `AGENTS.md` rule 4).
5. Do NOT add platform-specific procedure content to the top-level `SKILL.md` — see that file's own
   contribution note.
