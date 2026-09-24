# Notifications — Platform Index

One row per supported communications platform. Each platform directory holds its own `procedure.md`
(the full step-by-step, including formatting/rate-limit rules), plus wire-level `endpoints.md` where
the platform is REST/API-driven.

| Platform | Directory | Scope | Notes |
|---|---|---|---|
| _(none yet)_ | | | |

## Adding a new platform

1. Create `references/<platform>/procedure.md` following the shape of an existing single-vendor
   capability (e.g. `skills/cmdb_asset_management/references/servicenow/procedure.md`) as a
   template: Operational procedure → Patterns → Known limitations → Tools → Verification checklist.
2. Add `endpoints.md` (wire-level calls) alongside it, if the platform is REST/API-driven — a
   CLI-driven platform (unlikely for this capability, but possible for a self-hosted tool) keeps
   commands inline in `procedure.md` instead (see `AGENTS.md` rule 6).
3. State plainly which destination types (channel, thread, direct recipient) and content types
   (plain text, rich formatting, attachments) this platform actually supports — this capability is
   asymmetric across platforms by nature (see `SKILL.md`'s "Universal concepts" note), so don't
   assume parity with another platform already listed here.
4. Add one row to the table above, replacing the placeholder row.
5. Check every existing claim in `SKILL.md`'s "Universal concepts" section against this platform's
   actual behavior before assuming it holds — correct that section explicitly if it doesn't (see
   `AGENTS.md` rule 4).
6. Do NOT add platform-specific procedure content to the top-level `SKILL.md` — see that file's own
   contribution note.
