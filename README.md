# Agent Skills

Open source library of automation skills for AI agents working on network and infrastructure
operations.

## What This Is

Each skill teaches an agent how to carry out one operation — provisioning a DNS record, running a
change request to close, upgrading device software — reliably and consistently, regardless of which
vendor platform is behind it. The goal is a single, trustworthy place for an agent (or the team
building one) to find "how do we do X here" and get an answer, instead of every agent re-deriving it
from scratch or from a vendor's raw API docs.

## How It's Organized

Skills are organized by **capability**, not by vendor. A skill is one operation — DNS record
management, software upgrade lifecycle, change management, and so on — with every vendor's
implementation of that operation living underneath it as a reference, rather than as its own
top-level skill. A library with one skill per vendor doesn't scale to a domain with hundreds of
vendor × platform × version permutations; grouping by capability keeps the number of skills small
and stable while the reference material underneath grows.

```
skills/<capability>/
  SKILL.md                       # thin, stable router — the shared shape of the capability plus
                                  # an index pointer. Stays roughly constant size regardless of how
                                  # many vendors/platforms are added underneath.
  references/
    INDEX.md                     # table: vendor/platform -> directory -> scope -> notes
    <vendor>/
      procedure.md                # that vendor's full step-by-step — patterns, error handling,
                                   # tools, verification checklist
      endpoints.md                 # literal wire-level calls
```

For a large permutation space (e.g. software upgrades, where one vendor spans several platform
families and install modes), the same shape holds one level deeper:
`references/<vendor>/<platform-family>.md` — grouped by platform family/version line, not by every
individual model, unless behavior genuinely differs at that granularity.

**`SKILL.md` only changes for two reasons: a bug in the shared/router content, or a pointer to a
newly added vendor.** Every vendor-specific fact — a new error code, a corrected field, a changed API
path, a whole new procedure — goes in that vendor's own `references/<vendor>/` files. This keeps
`SKILL.md` small and stable no matter how many vendors accumulate, and keeps a PR that only touches
one vendor's behavior from ever touching the shared file.

## Current Skills

| Skill | Vendors covered |
|---|---|
| `dns_record_management` | Route 53, Azure DNS, Cloudflare, Infoblox NIOS, BlueCat Horizon, PowerDNS |
| `change_management` | ServiceNow, Freshservice, Jira Service Management, BMC Helix ITSM |
| `software_upgrade_lifecycle` | Cisco IOS-XE, Cisco IOS XR, Arista EOS, Juniper Junos EX, Extreme EXOS, NVIDIA Cumulus Linux |
| `ipam_allocation` | NetBox, Infoblox NIOS, SolarWinds IPAM |
| `cmdb_asset_management` | ServiceNow CMDB |
| `wireless_network_management` | Cisco Meraki, HPE Aruba Networking Central, Cisco Catalyst Center |

See each skill's `references/INDEX.md` for the full vendor/platform breakdown and scope notes.

## Using a Skill

These skills are framework-agnostic — plain markdown procedures meant to be read by any agent or
tool capable of loading a directory of instructions, not tied to a specific vendor's agent runtime.
Each `skills/<capability>/SKILL.md` is a self-contained router: point your agent's skill-loading
mechanism at a `skills/<capability>/` directory (or vendor it into your own project) to make that
capability available. If your framework has its own skills convention (for example, Claude Code's
`.claude/skills/<name>/SKILL.md` + `references/` layout), this repo's `skills/<capability>/`
directories already follow that same `SKILL.md`-plus-`references/` shape and can be dropped in
directly.

## Contributing

New skills and new vendor coverage are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for how this
repo is organized and the process for submitting changes. This project is governed by our
[Code of Conduct](CODE_OF_CONDUCT.md); report security issues per [SECURITY.md](SECURITY.md).
Contributions require agreement to the [CLA](CLA.md).

## License

<!-- PLACEHOLDER — license not yet decided; repo is private until this is settled. -->
Not yet finalized — see [LICENSE.md](LICENSE.md).

Copyright (c) 2026 Itential, Inc.
