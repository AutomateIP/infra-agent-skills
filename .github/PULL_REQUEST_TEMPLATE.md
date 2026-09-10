<!--
Thanks for contributing! Please fill out the sections below — see CONTRIBUTING.md for the full
process (fork-and-pull, branch naming, CLA, labels).
-->

## Summary

Brief description of what this PR does.

## Changes

- List of specific changes made
- Another change

## Skill(s) Affected

<!-- e.g. `dns_record_management` (new vendor: X) / `change_management` (SKILL.md correction) -->

## Testing

<!-- How was this verified? For a new/changed vendor procedure, this usually means: -->
- [ ] Every API call, field name, and endpoint path was checked against the vendor's current
      published documentation (link the source)
- [ ] If this corrects or contradicts an existing "universal concepts" assumption in `SKILL.md`,
      that correction is called out explicitly, not just folded into the vendor's own file
- [ ] `references/INDEX.md` for the affected skill is updated to list the new/changed entry

## Related Issues

Closes #123

## Checklist

- [ ] Branch is up to date with `main`
- [ ] New/changed vendor content lives under `references/<vendor>/`, not in the shared `SKILL.md`
      (unless this PR is specifically fixing a bug in the shared/router content)
- [ ] `SKILL.md` still stays thin — no vendor-specific detail leaked into it
- [ ] Every new task/companion file follows the repo's naming convention (see README) to avoid
      collisions with other vendors' files in the same directory
- [ ] Sign the Contributor License Agreement (CLA) — see [CLA.md](../CLA.md)
