---
name: container-image-build
description: Build and inspect a container image containing test code and its dependencies. Use when this operation is requested on Cisco pyATS Image Builder. Assumes a Linux host with an installed pyats-image-builder CLI, Python environment, and Docker Engine. This builds a local image. Registry publishing and workload execution are separate operations.
---

# Container Image Build

Build and inspect a container image containing test code and its dependencies using the selected platform's documented procedure.

## When to use this skill

Use for build and inspect a container image containing test code and its dependencies, on Cisco pyATS Image Builder.
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

This builds a local image. Registry publishing and workload execution are separate operations.

## Contributing to this skill

Change this router only to correct its shared concepts/routing or add a supported platform pointer.
Vendor details belong in that vendor's references. Recheck shared claims before adding a platform.

## Tools

Assumes a Linux host with an installed pyats-image-builder CLI, Python environment, and Docker Engine. The platform procedure lists the required operations or execution mechanism.

## Verification checklist

- [ ] Platform and operation match an entry in the index.
- [ ] The selected procedure's checks and any confirmation gates were followed.
- [ ] Actual results and documented failures are reported without replacing their status vocabulary.
