---
name: dhcp_scope_management
description: Create, read, update, and delete a DHCP scope/subnet and its lease reservations on a supported DHCP platform. Distinct from ipam_allocation, which reserves address space and IPs as inventory records; this skill manages the DHCP service configuration that actually leases addresses to clients from that space. Assumes an authenticated client for the target platform's API is already available; this skill does not provision or authenticate it. No vendor coverage yet — see references/INDEX.md.
argument-hint: "[platform] [action: create_scope|read_scope|update_scope|delete_scope|create_reservation|delete_reservation] [scope/reservation identifier, fields]"
---

# DHCP Scope Management

Manages DHCP scope/subnet configuration and lease reservations on whichever DHCP platform the caller
is targeting, following that platform's own documented procedure exactly.

## When to use this skill

- Creating, reading, updating, or deleting a DHCP scope/subnet, or creating/deleting a lease
  reservation for a specific client, on a supported platform.
- Not for reserving IP address space or an individual IP as an inventory record — see
  `ipam_allocation` for that. A platform like Infoblox may expose both concerns through related
  objects, but allocating the address as inventory and configuring DHCP to actually lease it are
  different operations; this skill is only the latter.

## Universal concepts (shared DHCP vocabulary, not a wire standard)

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
   away.

## Known limitations

- No vendor coverage yet — see `references/INDEX.md`. A platform not listed there needs a new
  reference file before this skill can be used for it.
- Does not cover IP address space allocation as an inventory record — see `ipam_allocation` for that.

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
      destructive changes (scope deletion, which affects every active lease under it)
- [ ] Outcome reported using the platform procedure's own status vocabulary
