---
name: device-session-recording
description: Record and replay device interactions or convert a recording into a mock CLI session. Use when this operation is requested on Cisco pyATS / Unicon. Assumes pyATS / Unicon and a test job; recording additionally needs an authenticated testbed. Playback reproduces recorded interactions; it does not emulate routing protocols or prove behavior of a live device.
---

# Device Session Recording

Record and replay device interactions or convert a recording into a mock CLI session using the selected platform's documented procedure.

## When to use this skill

Use for record and replay device interactions or convert a recording into a mock CLI session, on Cisco pyATS / Unicon.
The supported modes are listed in [references/INDEX.md](references/INDEX.md).

## Universal concepts

There is no cross-platform wire standard for this capability. Coverage and success criteria
are limited to the documented implementations in the index; no other platform behavior is implied.
A completed operation and the suitability of its result for a particular network are separate checks.

## Procedure

1. Identify the requested platform and supported mode in [references/INDEX.md](references/INDEX.md).
2. Open the indexed platform procedure and follow it exactly, including its mode-specific reference.
3. Report its observed output or result using that procedure's own status vocabulary.

## Known limitations

Playback reproduces recorded interactions; it does not emulate routing protocols or prove behavior of a live device.

## Contributing to this skill

Change this router only to correct its shared concepts/routing or add a supported platform pointer.
Vendor details belong in that vendor's references. Recheck shared claims before adding a platform.

## Tools

Assumes pyATS / Unicon and a test job; recording additionally needs an authenticated testbed. The platform procedure lists the required operations or execution mechanism.

## Verification checklist

- [ ] Platform and operation match an entry in the index.
- [ ] The selected procedure's checks and any confirmation gates were followed.
- [ ] Actual results and documented failures are reported without replacing their status vocabulary.
