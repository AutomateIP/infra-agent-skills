---
name: parallel-task-execution
description: Run independent callable invocations concurrently and collect their ordered results. Use when this operation is requested on Cisco pyATS. Assumes a Python environment with pyATS and an already-defined callable. This procedure does not determine whether device changes are independent or supply the operation executed by each worker.
---

# Parallel Task Execution

Run independent callable invocations concurrently and collect their ordered results using the selected platform's documented procedure.

## When to use this skill

Use for run independent callable invocations concurrently and collect their ordered results, on Cisco pyATS.
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

This procedure does not determine whether device changes are independent or supply the operation executed by each worker.

## Contributing to this skill

Change this router only to correct its shared concepts/routing or add a supported platform pointer.
Vendor details belong in that vendor's references. Recheck shared claims before adding a platform.

## Tools

Assumes a Python environment with pyATS and an already-defined callable. The platform procedure lists the required operations or execution mechanism.

## Verification checklist

- [ ] Platform and operation match an entry in the index.
- [ ] The selected procedure's checks and any confirmation gates were followed.
- [ ] Actual results and documented failures are reported without replacing their status vocabulary.
