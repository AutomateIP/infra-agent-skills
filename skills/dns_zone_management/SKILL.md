---
name: dns_zone_management
description: Create, read, update, and delete a DNS zone — including its SOA/NS records and subdomain delegation — on a supported DNS platform. Distinct from dns_record_management, which manages individual records inside a zone and assumes the zone already exists; this skill is the zone-level lifecycle those record operations depend on. Assumes an authenticated API/WAPI client for the target platform is already available; this skill does not provision or authenticate it. No vendor coverage yet — see references/INDEX.md.
argument-hint: "[platform] [action: create_zone|read_zone|update_zone|delete_zone|delegate_subdomain] [zone name, fields]"
---

# DNS Zone Management

Manages DNS zone lifecycle — creation, SOA/NS configuration, subdomain delegation, deletion — on
whichever DNS platform the caller is targeting, following that platform's own documented procedure
exactly.

## When to use this skill

- Creating, reading, updating, or deleting a zone, or delegating a subdomain to another set of
  nameservers, on a supported platform.
- Not for creating/updating/deleting an individual record inside a zone that already exists — see
  `dns_record_management` for that. This skill's job ends where a zone exists and is correctly
  configured; record-level content inside it is the other skill's concern.

## Universal concepts (shared DNS zone vocabulary — RFC 1035/1996/2136)

*(To be filled in as vendors are added — see `AGENTS.md` rule 4: only claims verified true across
every vendor actually listed belong here. Do not pre-populate this section with assumptions before a
vendor's real behavior has been checked against them. Note that `dns_record_management` already
grounds its own universal concepts in RFC 1035/2136/1123 — zone-level concerns like SOA serial
semantics and NS delegation draw on the same RFC family but are a distinct set of claims to verify,
not an inherited given.)*

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
- Does not cover individual record management inside an existing zone — see `dns_record_management`
  for that.

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
      destructive changes (zone deletion, which removes every record inside it)
- [ ] Outcome reported using the platform procedure's own status vocabulary
