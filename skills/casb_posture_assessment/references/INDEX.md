# CASB Posture Assessment — Vendor Index

One row per supported vendor. Each vendor directory holds its own `procedure.md` — a read/report
path organized by assessment type (shadow-IT discovery, OAuth-grant risk, policy-violation events,
risk score), not a linear CRUD sequence. See `SKILL.md`'s shape note before authoring a new vendor
file here.

| Vendor / Platform | Directory | Assessment types covered | Notes |
|---|---|---|---|
| _(none yet)_ | | | |

## Adding a new vendor

1. Create `references/<vendor>/procedure.md` organized by assessment type — not a CRUD-style linear
   procedure. Each section should map a real, vendor-documented API/report output to the finding
   categories that platform actually reports.
2. Quote the literal API paths or report fields used for each assessment type in `endpoints.md`
   (this skill is typically REST-API-driven, unlike the CLI-driven troubleshooting skills — check
   the specific platform before assuming either mechanism).
3. If this vendor also appears in `casb_policy_management`'s `INDEX.md`, note that in this row's
   Notes column.
4. Add one row to the table above, replacing the placeholder row — note which assessment types this
   vendor's file actually covers; a vendor entry doesn't need to cover all four in its first
   version.
5. Check every existing claim in `SKILL.md`'s "Universal concepts" section against this vendor's
   actual behavior before assuming it holds (see `AGENTS.md` rule 4).
6. Do NOT add vendor-specific procedure content to the top-level `SKILL.md` — see that file's own
   contribution note.
