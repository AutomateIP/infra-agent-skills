---
name: load_balancing
description: Create, read, update, and delete load-balancer virtual server (VIP) and pool/pool-member objects on a supported load-balancing platform. A VIP configuration often references a certificate by identifier for TLS termination — provisioning that certificate is certificate_management's job, not this skill's; this skill only consumes the reference. Assumes an authenticated client for the target platform's API/CLI is already available; this skill does not provision or authenticate it. No vendor coverage yet — see references/INDEX.md.
argument-hint: "[platform] [action: create_vip|read_vip|update_vip|delete_vip|add_pool_member|remove_pool_member] [VIP/pool identifier, fields]"
---

# Load Balancing

Manages load-balancer virtual server (VIP) and pool/pool-member objects on whichever load-balancing
platform the caller is targeting, following that platform's own documented procedure exactly.

## When to use this skill

- Creating, reading, updating, or deleting a virtual server (VIP), or adding/removing/updating a pool
  member's state, on a supported platform.
- Not for issuing or renewing the TLS certificate a VIP terminates — see `certificate_management` for
  that. A VIP may *reference* a certificate by identifier, but provisioning that certificate is out of
  scope here.

## Universal concepts (shared load-balancing vocabulary, not a wire standard)

*(To be filled in as vendors are added — see `AGENTS.md` rule 4: only claims verified true across
every vendor actually listed belong here. Do not pre-populate this section with assumptions before a
vendor's real behavior has been checked against them.)*

See `references/INDEX.md` for which platforms have a procedure, and each platform file for how it
implements this shape concretely.

## Procedure

1. **Identify the target platform** — from the caller's request or the `platform` argument. Check
   `references/INDEX.md`; if no matching row exists, stop: this skill doesn't cover it yet.
2. **Open `references/<platform>/procedure.md`** and follow it exactly — it contains that platform's
   full operational procedure, error handling, patterns, known limitations, tools, and verification
   checklist.
3. **Report the outcome** using that platform procedure's own status vocabulary — don't normalize it
   away, and don't conflate a pool member being disabled/drained with it being removed, on a platform
   that distinguishes them.

## Known limitations

- No vendor coverage yet — see `references/INDEX.md`. A platform not listed there needs a new
  reference file before this skill can be used for it.
- Does not cover certificate issuance/renewal, even where a VIP configuration references a
  certificate by identifier — see `certificate_management` for that.

## Contributing to this skill

**`SKILL.md` only changes for two reasons: a bug in the universal concepts/router logic above, or
adding a pointer to a newly-supported platform.** Platform-specific procedure changes — a new error
code, a corrected field, a changed call mechanism detail — belong in that platform's own
`references/<platform>/procedure.md`, never here.

## Tools

Each platform assumes its own authenticated client — see `references/INDEX.md` for which, and that
platform's `procedure.md` for the specific operations.

## Verification checklist

- [ ] Target platform confirmed against `references/INDEX.md` before proceeding
- [ ] That platform's own `procedure.md` was followed, including its own confirmation gates on
      destructive changes (VIP deletion, pool member removal from a live pool)
- [ ] Outcome reported using the platform procedure's own status vocabulary, distinguishing a
      disabled/drained pool member from a removed one where the platform makes that distinction
