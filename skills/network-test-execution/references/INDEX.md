# Network Test Execution — Platform Index

| Vendor / platform | Directory | Scope | Notes |
|---|---|---|---|
| Cisco pyATS / AEtest | [cisco_pyats/](cisco_pyats/procedure.md) → [aetest.md](cisco_pyats/aetest.md) | Prepare and run Python tests through Easypy; inspect results | Explicit test assertions; setup/test/cleanup effects require review |
| Cisco Genie / Blitz | [cisco_pyats/](cisco_pyats/procedure.md) → [blitz.md](cisco_pyats/blitz.md) | Prepare and run a named YAML testcase; inspect action results | Dedicated Blitz schema validation is not promised |
| Cisco Genie / Robot Framework | [cisco_pyats/](cisco_pyats/procedure.md) → [robot.md](cisco_pyats/robot.md) | Prepare and execute keyword suites; inspect Robot results | Optional libraries must be installed |
| Cisco Genie harness | [cisco_pyats/](cisco_pyats/procedure.md) → [genie-harness.md](cisco_pyats/genie-harness.md) | Run approved trigger/verification datafiles | Trigger implementations and platform requirements are inputs |
| Cisco pyATS Health Check | [cisco_pyats/](cisco_pyats/procedure.md) → [health.md](cisco_pyats/health.md) | Run built-in/custom health processors within a job | No standalone monitoring; clearing/deletion options are flagged |
| Cisco XPRESSO | [cisco_xpresso/](cisco_xpresso/procedure.md) | Submit one registered job/profile and inspect its request results | Authenticated UI; registration, scheduling, bundles, and deployment excluded |

Companion files use each mode's basename (`aetest-endpoints.md`, for example). The pyATS
`procedure.md` routes to the selected complete mode procedure. Native result vocabularies remain
platform-specific; queue acceptance does not establish that a test passed.
