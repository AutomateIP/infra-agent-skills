---
name: certificate_management
description: Issue, renew, read, and revoke TLS/SSL certificates on a supported certificate-management platform or protocol (a public/private CA, an ACME-based issuer, or a certificate-management API). Use when an agent needs to manage certificate lifecycle itself, distinct from installing/binding an already-issued certificate to a specific application, device, or load-balancer VIP (that binding step belongs to the consuming platform's own skill, e.g. load_balancing). Assumes an authenticated client/credential for the target platform's API is already available; this skill does not provision or authenticate it. No vendor/protocol coverage yet — see references/INDEX.md.
argument-hint: "[platform] [action: issue|renew|read|revoke] [certificate identifier/CSR, fields]"
---

# Certificate Management

Manages TLS/SSL certificate lifecycle — issuance, renewal, inspection, revocation — on whichever
certificate-management platform or protocol the caller is targeting, following that platform's own
documented procedure exactly.

## When to use this skill

- Issuing, renewing, reading (expiry, SANs, chain), or revoking a certificate on a supported
  platform/protocol.
- Not for installing or binding an already-issued certificate to a specific consuming system — e.g.
  binding a certificate to a load-balancer VIP is `load_balancing`'s concern, not this skill's; this
  skill's job ends at producing/managing the certificate itself.

## Universal concepts (shared certificate-management vocabulary, not a wire standard)

*(To be filled in as vendors/protocols are added — see `AGENTS.md` rule 4: only claims verified true
across every vendor actually listed belong here. Do not pre-populate this section with assumptions
before a vendor's real behavior has been checked against them.)*

See `references/INDEX.md` for which platforms/protocols have a procedure, and each platform file for
how it implements this shape concretely.

## Procedure

1. **Identify the target platform/protocol** — from the caller's request or the `platform` argument.
   Check `references/INDEX.md`; if no matching row exists, stop: this skill doesn't cover it yet.
2. **Open `references/<platform>/procedure.md`** and follow it exactly — it contains that platform's
   full operational procedure, error handling, patterns, known limitations, tools, and verification
   checklist.
3. **Report the outcome** using that platform procedure's own status vocabulary — don't normalize it
   away, and don't conflate a renewed certificate with a newly issued one, on a platform that
   distinguishes them (e.g. serial number/identifier continuity).

## Known limitations

- No vendor/protocol coverage yet — see `references/INDEX.md`. A platform not listed there needs a
  new reference file before this skill can be used for it.
- Does not cover installing/binding an issued certificate to a consuming application, device, or load
  balancer — that belongs to the consuming system's own skill.

## Contributing to this skill

**`SKILL.md` only changes for two reasons: a bug in the universal concepts/router logic above, or
adding a pointer to a newly-supported platform.** Platform-specific procedure changes — a new error
code, a corrected field, a changed call mechanism detail — belong in that platform's own
`references/<platform>/procedure.md`, never here.

## Tools

Each platform assumes its own authenticated client/credential — see `references/INDEX.md` for which,
and that platform's `procedure.md` for the specific operations.

## Verification checklist

- [ ] Target platform/protocol confirmed against `references/INDEX.md` before proceeding
- [ ] That platform's own `procedure.md` was followed, including its own confirmation gates on
      destructive changes (revocation, which is generally irreversible)
- [ ] Outcome reported using the platform procedure's own status vocabulary, distinguishing a renewed
      certificate from a newly issued one where the platform makes that distinction
