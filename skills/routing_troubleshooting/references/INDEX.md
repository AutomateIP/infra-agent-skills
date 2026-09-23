# Routing Troubleshooting (BGP) — Vendor Index

One row per supported vendor. Each vendor directory holds its own `procedure.md` — a decision tree
organized by symptom (session down, session flapping, route missing, unexpected best-path), not a
linear operation sequence. See `SKILL.md`'s "Why this skill has a different shape than the others in
this repo" before authoring a new vendor file here.

| Vendor / Platform | Directory | Symptoms covered | Notes |
|---|---|---|---|
| Cisco IOS-XE | `cisco-iosxe/` | session_down, unexpected_best_path | Does not yet cover session_flapping or route_missing — the sources reached in this pass (a Cisco troubleshooting tech note and Cisco's best-path-selection doc) didn't have enough independently-verified detail for those two; see the file's own "Explicit unknowns" section. The IOS-XE command-reference page for `show ip bgp summary`'s full field dictionary could not be reached this pass either. |
| Juniper Junos | `juniper-junos/` | session_down, session_flapping | Does not yet cover route_missing or unexpected_best_path — Junos's own active-route-selection/tie-break and "hidden route" documentation pages could not be reached this pass (repeated 404s on guessed URLs); do not substitute another vendor's best-path order for Junos's own, since it is documented as its own process. Confirms the RFC 4271 session-state names are used as-is (Idle/Connect/Active/OpenSent/OpenConfirm/Established) with no naming divergence found in the two command-reference pages consulted. |
| FRRouting | `frrouting/` | session_down, route_missing, unexpected_best_path | Does not yet cover session_flapping as its own entry point — the consulted doc's flap-related content (dampening, MED oscillation) is scattered under other topics rather than a dedicated flapping narrative. Sourced from FRR's "latest" docs branch, not pinned to one release — version-sensitive defaults (`bgp ebgp-requires-policy`, `bgp enforce-first-as`, `bgp network import-check`) should be reconfirmed against the caller's actual installed version. FRR's own best-path order diverges from Cisco's by inserting an admin-distance check first (step 0) and an "already-selected external path" stability tiebreaker (step 10) that Cisco's documented order doesn't have — see SKILL.md's "Universal concepts" note on this. |

## Adding a new vendor

1. Create `references/<vendor>/procedure.md` as a decision tree, one entry point per symptom category
   (session down, session flapping, route missing, unexpected best-path) — not a CRUD-style linear
   procedure. Each branch should map an observed state (from a real, vendor-documented CLI/API output)
   to either a documented likely cause, a next check to run, or an explicit "consult platform-specific
   guidance" when the source is silent.
2. Quote the literal CLI/API commands used for each check, and any documented error/state strings
   worth pattern-matching on, directly in `procedure.md` (BGP troubleshooting is read-only and
   CLI/API-session-driven, so this does not need a separate `endpoints.md` the way REST-CRUD skills
   do — see `AGENTS.md` rule 6's CLI-driven guidance).
3. Add one row to the table above, replacing the placeholder row — note which symptom categories this
   vendor's file actually covers; a vendor entry doesn't need to cover all four symptoms in its first
   version.
4. Check every existing claim in `SKILL.md`'s "Universal concepts" section (grounded in RFC 4271's
   session state machine) against this vendor's actual CLI/API terminology before assuming it maps
   cleanly — a vendor's own state names diverging from the RFC's is itself worth documenting, not
   silently normalizing away (see `AGENTS.md` rule 4).
5. Do NOT add vendor-specific decision-tree content to the top-level `SKILL.md` — see that file's own
   contribution note.
