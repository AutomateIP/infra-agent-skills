---
name: device-api-connection
description: Establish a device REST session, perform a documented read, and disconnect. Use when this operation is requested on Cisco pyATS REST connector for DNAC. Assumes pyATS, rest.connector, and authorized DNAC credentials. Only the DNAC connector read sequence is covered; arbitrary REST services, custom connectors, and controller mutations are excluded.
---

# Device API Connection

Establish a device REST session, perform a documented read, and disconnect using the selected platform's documented procedure.

## When to use this skill

Use for establish a device REST session, perform a documented read, and disconnect, on Cisco pyATS REST connector for DNAC.
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

Only the DNAC connector read sequence is covered; arbitrary REST services, custom connectors, and controller mutations are excluded.

## Contributing to this skill

Change this router only to correct its shared concepts/routing or add a supported platform pointer.
Vendor details belong in that vendor's references. Recheck shared claims before adding a platform.

## Tools

Assumes pyATS, rest.connector, and authorized DNAC credentials. The platform procedure lists the required operations or execution mechanism.

## Verification checklist

- [ ] Platform and operation match an entry in the index.
- [ ] The selected procedure's checks and any confirmation gates were followed.
- [ ] Actual results and documented failures are reported without replacing their status vocabulary.
