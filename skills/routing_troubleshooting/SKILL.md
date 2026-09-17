---
name: routing_troubleshooting
description: Diagnose BGP session and route-propagation problems (session not establishing, session flapping, expected route missing from the table, unexpected best-path selection) by reading session and route state on a supported routing platform, and mapping observed state to the platform's own documented likely causes. Use when an agent needs to investigate why BGP isn't behaving as expected — not for configuring BGP, static routes, or routing policy from scratch (that is a create/update operation with a different shape; this skill is read-only diagnosis). Assumes an authenticated CLI/API session against the target device or controller is already available; this skill does not provision or authenticate it. No vendor coverage yet — see references/INDEX.md.
argument-hint: "[platform] [symptom: session_down|session_flapping|route_missing|unexpected_best_path] [peer/prefix identifier]"
---

# Routing Troubleshooting (BGP)

Diagnoses BGP session and route-propagation problems on whichever routing platform the caller is
targeting, by reading session/route state and mapping it to that platform's own documented likely
causes — not a configuration-change skill.

## Why this skill has a different shape than most others in this repo

Most skills in this repository are CRUD-shaped: create, read, update, or delete one record on a
platform, with one clear success/failure outcome per call. **BGP troubleshooting is a diagnostic
procedure, not a CRUD operation** — the "procedure" is a decision tree (check session state → if
Established, check whether routes are received → if received, check whether they're best-path
selected → …), and the useful output is a diagnosis plus supporting evidence, not a single created or
deleted record. `interface_link_troubleshooting` shares this same diagnostic shape (and points back
here rather than duplicating this explanation) — treat the two as establishing one repeatable
diagnostic-skill pattern, not a one-off exception. Do not force this skill's
`references/<platform>/procedure.md` files into the CRUD-style "operation → wire call → success
criterion" shape the other capabilities use — a diagnostic step's "success criterion" is "the
operator/caller now knows which documented cause
applies," not a state transition on the platform.

This also means: **this skill has no destructive-action confirmation gates**, unlike every CRUD
skill in this repo (rule 7 of `AGENTS.md` doesn't apply the same way, because reading session/route
state doesn't change anything on the platform). If a future addition to this skill ever proposes a
remediation *action* (clearing a session, resetting a peer) rather than a read, that action needs its
own confirmation gate exactly like any other skill's destructive step — don't let troubleshooting
scope creep into unguarded remediation.

## When to use this skill

- A BGP session won't establish, is flapping, or a specific prefix is missing/unexpectedly
  not-best-path on a supported platform, and the caller needs to identify which documented cause
  applies.
- Not for configuring BGP neighbors, policies, or static routes from scratch — that would be a
  CRUD-shaped configuration capability with its own confirmation-gate requirements, separate from
  this read-only diagnostic one.

## Universal concepts (shared BGP vocabulary, genuinely grounded — RFC 4271)

*(To be filled in as vendors are added. Unlike the CRUD-shaped skills' "Universal concepts," this
section can lean on RFC 4271's actual state-machine vocabulary — Idle/Connect/Active/OpenSent/
OpenConfirm/Established — as a real wire-level standard, the way DNS leans on RFC 1035. Still verify
every claim against each vendor actually listed before asserting it as universal — a vendor's CLI/API
terminology for the same RFC state can differ, and that gap itself is worth documenting, not papered
over.)*

See `references/INDEX.md` for which platforms have a procedure, and each platform file for how it
implements this shape concretely.

## Procedure

1. **Identify the target platform** — from the caller's request or the `platform` argument. Check
   `references/INDEX.md`; if no matching row exists, stop: this skill doesn't cover it yet.
2. **Identify the symptom category** — session not establishing, session flapping, route missing, or
   unexpected best-path selection. This determines which decision-tree entry point in
   `references/<platform>/procedure.md` to start from; the platform file is organized by symptom, not
   by a linear operation sequence.
3. **Open `references/<platform>/procedure.md`** and follow its decision tree for that symptom exactly
   — it maps each observed state to that platform's own documented likely causes and the next check to
   run.
4. **Report the diagnosis** — which documented cause the evidence points to, the evidence itself (the
   literal command/API output that supports it), and any documented next step the platform's own
   guidance suggests (which is often "escalate" or "consult platform-specific guidance," not a fix this
   skill performs) — don't present a diagnosis without the evidence that supports it.

## Known limitations

- No vendor coverage yet — see `references/INDEX.md`. A platform not listed there needs a new
  reference file before this skill can be used for it.
- Read-only diagnosis only. Does not configure, remediate, or reset anything — a caller who needs to
  act on a diagnosis (clear a session, change a policy) needs a separate configuration-change
  capability, which may not exist yet in this repository.
- Covers BGP only. OSPF, IS-IS, or static-route troubleshooting would be a separate capability (or a
  clearly-scoped addition here with its own vendor coverage) — do not silently expand this skill's
  scope to other protocols without treating that as a new-capability-shaped decision.

## Contributing to this skill

**`SKILL.md` only changes for two reasons: a bug in the universal concepts/router logic above, or
adding a pointer to a newly-supported platform.** Platform-specific decision-tree content — a new
documented cause, a corrected CLI/API output pattern, a vendor-specific state name — belongs in that
platform's own `references/<platform>/procedure.md`, never here.

## Tools

Each platform assumes its own authenticated CLI/API session — see `references/INDEX.md` for which,
and that platform's `procedure.md` for the specific read-only checks it uses.

## Verification checklist

- [ ] Target platform confirmed against `references/INDEX.md` before proceeding
- [ ] Symptom category correctly identified before opening the platform's decision tree
- [ ] That platform's own `procedure.md` decision tree was followed for the identified symptom
- [ ] Diagnosis reported together with the literal evidence (command/API output) that supports it —
      never a bare conclusion
- [ ] No configuration change was made — this skill is read-only; anything beyond reading state is out
      of scope here
