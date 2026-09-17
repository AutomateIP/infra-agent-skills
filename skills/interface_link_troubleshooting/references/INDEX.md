# Interface / Link Troubleshooting — Vendor Index

One row per supported vendor. Each vendor directory holds its own `procedure.md` — a decision tree
organized by symptom (interface down, flapping, high errors/discards, duplex/speed mismatch, no link
light), not a linear operation sequence. See `SKILL.md`'s shape note (and `routing_troubleshooting`'s
fuller explanation, which this skill shares) before authoring a new vendor file here.

| Vendor / Platform | Directory | Symptoms covered | Notes |
|---|---|---|---|
| _(none yet)_ | | | |

## Adding a new vendor

1. Create `references/<vendor>/procedure.md` as a decision tree, one entry point per symptom category
   (interface down, flapping, high errors/discards, duplex/speed mismatch, no link light) — not a
   CRUD-style linear procedure. Each branch should map an observed state (from a real,
   vendor-documented CLI/API output) to either a documented likely cause, a next check to run, or an
   explicit "consult platform-specific guidance" when the source is silent.
2. Quote the literal CLI/API commands used for each check, and any documented error/state strings
   worth pattern-matching on, directly in `procedure.md` (this skill is read-only and
   CLI/API-session-driven, so it does not need a separate `endpoints.md` the way REST-CRUD skills do
   — see `AGENTS.md` rule 6's CLI-driven guidance).
3. Add one row to the table above, replacing the placeholder row — note which symptom categories this
   vendor's file actually covers; a vendor entry doesn't need to cover all five symptoms in its first
   version.
4. Check every existing claim in `SKILL.md`'s "Universal concepts" section against this vendor's actual
   CLI/API terminology before assuming it holds — there is no wire-level standard grounding interface
   state the way RFC 4271 grounds BGP, so be especially careful not to assume a claim is universal
   just because it held for the first vendor added (see `AGENTS.md` rule 4).
5. Do NOT add vendor-specific decision-tree content to the top-level `SKILL.md` — see that file's own
   contribution note.
