---
name: vpn_tunnel_management
description: Create, read, update, and delete site-to-site and remote-access VPN tunnel configurations (IPsec/IKE phase 1 and phase 2 parameters, tunnel interfaces, remote-access profiles) on a supported platform. Assumes an authenticated client for the target platform's API/CLI is already available; this skill does not provision or authenticate it. No vendor coverage yet — see references/INDEX.md.
argument-hint: "[platform] [action: create_tunnel|read_tunnel|update_tunnel|delete_tunnel] [tunnel identifier, fields]"
---

# VPN Tunnel Management

Manages site-to-site and remote-access VPN tunnel configuration on whichever platform the caller is
targeting, following that platform's own documented procedure exactly.

## When to use this skill

- Creating, reading, updating, or deleting a site-to-site tunnel (IPsec/IKE phase 1 and phase 2
  parameters, tunnel interface) or a remote-access VPN profile, on a supported platform.

## Universal concepts (shared VPN vocabulary — IKE/IPsec, RFC 7296 and related)

*(To be filled in as vendors are added — see `AGENTS.md` rule 4: only claims verified true across
every vendor actually listed belong here. Do not pre-populate this section with assumptions before a
vendor's real behavior has been checked against them. IKEv2 is grounded in RFC 7296 as a real wire
standard — similar to how `dns_record_management` grounds its universal concepts in RFC 1035 — but
still verify every claim against each vendor's actual implementation before asserting it as
universal; vendor-specific proposal/transform-set naming conventions vary even where the underlying
IKE negotiation is standard.)*

See `references/INDEX.md` for which platforms have a procedure, and each platform file for how it
implements this shape concretely.

## Procedure

1. **Identify the target platform** — from the caller's request or the `platform` argument. Check
   `references/INDEX.md`; if no matching row exists, stop: this skill doesn't cover it yet.
2. **Open `references/<platform>/procedure.md`** and follow it exactly — it contains that platform's
   full operational procedure, error handling, patterns, known limitations, tools, and verification
   checklist.
3. **Report the outcome** using that platform procedure's own status vocabulary — don't normalize it
   away, and don't conflate a tunnel configuration being written with the tunnel actually establishing
   (phase 1/phase 2 negotiation state is a separate fact to confirm from configuration success).

## Known limitations

- No vendor coverage yet — see `references/INDEX.md`. A platform not listed there needs a new
  reference file before this skill can be used for it.
- Does not cover firewall rules permitting the tunnel's traffic — see `firewall_management` for that;
  a tunnel may be correctly configured and still blocked by policy this skill does not manage.

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
      destructive changes (tunnel deletion, which drops active traffic through it)
- [ ] Outcome reported using the platform procedure's own status vocabulary, distinguishing
      configuration success from actual tunnel-up/negotiated state where the platform makes that
      distinction
