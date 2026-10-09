# pyATS adaptation map

This contribution adapts the 17 topic references in
[automateyournetwork/pyATS_skills](https://github.com/automateyournetwork/pyATS_skills/tree/f9a1cacdb3cd528c9c16ea9c4e1b7aec5c59d9f9)
(commit `f9a1cacdb3cd528c9c16ea9c4e1b7aec5c59d9f9`) to this repository's capability structure.
It adds **10 capabilities** and extends **2 existing capabilities**. The root catalogue therefore
contains **36 capabilities**, including its existing scaffolds.

The source repository supplies the requested coverage map, not the authority for platform behavior.
Procedural facts were checked against Cisco's documentation and rewritten. Citations and retrieval
dates are in the relevant references; raw research and local test artifacts remain outside the repo.
The source project identifies its license as Apache-2.0. No source skill files, vendor prose, or
source-repository example blocks were copied wholesale into this contribution.

## Topic destinations

| Source topic | Capability / reference | Adaptation boundary |
|---|---|---|
| `testbeds` | [testbed-management](../skills/testbed-management/SKILL.md) | YAML loading/validation, dictionary loading, and direct topology construction; inventory APIs remain separate |
| `secret-strings` | [credential-encoding](../skills/credential-encoding/SKILL.md) | Explicit Fernet setup, runtime markup, masking, and plaintext exposure |
| `learn` | [network-state-collection](../skills/network-state-collection/SKILL.md) | Supported feature collection and snapshot comparison |
| `parse` | [network-state-collection](../skills/network-state-collection/SKILL.md) | Command collection and captured-text parsing; custom parser development excluded |
| `execute-configure` | [device-cli-execution](../skills/device-cli-execution/SKILL.md) | Approved payload transport; platform-specific commit/verification remain explicit inputs |
| `pcall` | [parallel-task-execution](../skills/parallel-task-execution/SKILL.md) | Argument pairing, ordered results, child failures, and timeouts |
| `aetest` | [AEtest mode](../skills/network-test-execution/references/cisco_pyats/aetest.md) | Script/job preparation, execution, and native results |
| `blitz` | [Blitz mode](../skills/network-test-execution/references/cisco_pyats/blitz.md) | Named testcases and action execution; advanced actions require their specific documentation |
| `robot-framework` | [Robot mode](../skills/network-test-execution/references/cisco_pyats/robot.md) | Keyword suite preparation/execution; no company-specific choice of test framework |
| `genie-triggers-validations` | [Genie harness mode](../skills/network-test-execution/references/cisco_pyats/genie-harness.md) | Mapping and explicit trigger/verification selection; no invented trigger library |
| `clean` | [device-reprovisioning](../skills/device-reprovisioning/SKILL.md) | Validate/run an approved platform-specific Clean; no universal erase/install recipe |
| `mock-devices` | [device-session-recording](../skills/device-session-recording/SKILL.md) | Record, replay, convert, and launch a mock CLI |
| `rest-connector` | [device-api-connection](../skills/device-api-connection/SKILL.md) | DNAC read-session lifecycle only; arbitrary controllers and custom connectors excluded |
| `webex` | [existing Webex notifications](../skills/notifications/references/cisco_webex/procedure.md) | Documented post-job reporter preparation feeding the existing message-create sequence |
| `containers` | [pyATS container execution](../skills/container_workload_deployment/references/cisco_pyats/procedure.md), [container-image-build](../skills/container-image-build/SKILL.md) | Published image execution and local image build are separate capabilities |
| `health-check` | [Health Check mode](../skills/network-test-execution/references/cisco_pyats/health.md) | Job-integrated checks only; copying, log clearing, and file deletion are explicit effects |
| `xpresso` | [XPRESSO mode](../skills/network-test-execution/references/cisco_xpresso/procedure.md) | One registered job/profile request and its results; deployment, scheduling, bundles, and administration excluded |

The supporting source topics (testbed schema, secret-string detail, AEtest example, and Blitz
catalogue) informed the corresponding binding references. They do not become separate capabilities.

## Corrections verified during adaptation

- The default secret-string representer obscures values; encrypted storage requires explicit Fernet
  configuration. Decoding prints plaintext and is identified as a private verification step.
- Testbeds are not universally required for every pyATS operation. Local parallel calls and
  self-contained AEtest assertions, for example, do not intrinsically require a live device inventory.
- Genie CLI parsing has prefix/fuzzy options, and its output argument names a directory. The
  unsupported claim that every parser entry point uses exact-only matching was removed.
- Feature coverage and populated model fields are not guaranteed across all operating systems.
  The source's generic offline-learn signature was not carried over without documentation.
- Unicon initialization can change device settings. Configure/commit behavior is plugin-specific,
  and error-pattern detection can be disabled; absence of an exception is insufficient verification.
- AEtest result rollup follows a table, not the source's approximate severity ordering. The example
  now has a complete job import and a real assertion without inventing an OSPF-neighbor count path.
- Blitz trigger datafiles use top-level testcase names. Placeholder APIs and unverified diff-action
  parameters were removed.
- Genie verification iteration is a fixed interval, not exponential backoff.
- Clean uses a cleaner declaration, stage mappings, and an order list. The integrated job sequence
  includes `--invoke-clean`; a connection-only schema example is not presented as a full reset.
- A mock CLI is spawned through a Unicon connection. The source's direct MockDevice connection-class
  substitution was replaced, and the documented Pcall recording limitation is retained.
- REST implementation selection uses supported platform plugins. No automatic token-refresh,
  pagination, universal credential inheritance, or generic all-platform HTTP behavior is promised.
- Health Check's integrated mode is distinguished from standalone monitoring. Crashinfo copying and
  optional deletion are documented effects, not automatically read-only checks.

The procedures also flag defects in Cisco's examples where relevant: the DNAC example's omitted OS
selector, an unlearned second Ops object in a comparison snippet, and malformed Robot headings.

## Router and naming decisions

Vendor wiring stays under `references/`; Python SDK bindings are treated as wiring as well as REST
calls. CLI commands remain inline. The five pyATS execution modes use basename-prefixed companions,
such as `aetest-endpoints.md`. XPRESSO has its own platform directory.

Of the pre-existing routers, only `container_workload_deployment/SKILL.md` changes: it adds the
Cisco pyATS implementation/client assumption and removes the now-stale three-platform count.
The image-reference, scaling, and registry concepts still hold for this Docker-based implementation;
no new universal platform behavior was inferred. The existing notifications router is unchanged
because result-message preparation stays within its existing Webex send scope.

New capability directories and frontmatter use matching hyphenated names, as required by the
Agent Skills specification and the skill-creator validator. Existing capability names remain
unchanged. This preserves the capability/vendor layout without propagating the existing
underscore naming incompatibility into new skills.

## Validation scope

Validation uses synthetic local inputs and the installed pyATS / Genie / Unicon 25.2 packages.
The references were researched against Cisco's rolling documentation on 2026-10-09; local checks
against 25.2 do not prove availability of every feature described by newer documentation.

Local checks covered testbed schema/loading and direct object construction, rejection of invalid
input, Clean schema and health-datafile validation, offline parsing without device execution, diff exclusions, parallel-call ordering/truncation,
child exceptions/timeouts, AEtest passing and failing assertions with cleanup, and Robot keyword
resolution in dry-run mode. Python/YAML examples were parsed and local links were checked. The
published source links added for these procedures were reachable.

No live devices, controller API sessions, Webex sends, Clean runs, Docker builds, or XPRESSO jobs
were executed. Platform-dependent inputs and explicitly unverified bindings remain marked in their
own procedure or wiring reference.
