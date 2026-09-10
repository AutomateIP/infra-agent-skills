# AGENTS.md

Instructions for any agent (or human) authoring, reviewing, or extending content in this
repository. This applies to every `skills/<capability>/` directory, whether the change is a brand
new skill, a new vendor under an existing one, or a correction to existing content.

If you are an agent asked to add or fix a skill here, follow this file exactly — it is the
contribution contract for this repo, not a suggestion. However you produced the content, the
mechanism is immaterial; what matters is that the result satisfies every rule below before it's
opened as a PR.

## What a skill is (and isn't) here

A skill in this repo teaches an agent how to carry out **one deterministic, approved procedure** —
creating a DNS record, running a device software upgrade, closing a change request — the same way
every time, regardless of which vendor platform sits behind it. It is grounded in that vendor's own
approved documentation, not business judgment.

**The test:** could two different engineers at two different companies use this skill unmodified?
If yes, it belongs here. If the answer depends on "well, it depends on our ticketing process / our
approval chain / our internal naming convention," it's a business-specific agent prompt, not a
skill — it doesn't belong in this repo.

Do not include: a role/persona framing, customer-specific naming or terminology, or business
judgment calls presented as if they were universal.

## Repository shape

```
skills/<capability>/
  SKILL.md                       # thin, stable router
  references/
    INDEX.md                     # vendor/platform -> directory -> scope -> notes
    <vendor>/
      procedure.md                # full step-by-step for that vendor
      endpoints.md                 # literal wire-level calls
```

Your own local research notes for a vendor (not part of the repository shape — never committed;
see rule 1) can live wherever you keep them, entirely outside this directory tree.

See the [README](README.md) for the full rationale. The rest of this file assumes you understand
why skills are organized by capability, not by vendor.

## Authoring rules

These apply whether you're writing a brand-new `skills/<capability>/SKILL.md`, adding a vendor
under `references/<vendor>/`, or correcting existing content.

### 1. Ground every fact in the vendor's own approved documentation — always in your own words

- Use the vendor's own approved docs (official manual, API reference, admin guide) as the primary
  source — not a third-party summary, blog post, or your own general knowledge of the product. If
  a secondary source was genuinely necessary (primary blocked, paywalled, or the content doesn't
  exist there), say so explicitly in your local research notes and flag it in the PR — don't
  launder it as if it came from the primary source.
- **Every fact must be restated in your own original wording — never quote, paste, or closely
  mirror a vendor's own sentence structure or phrasing.** This applies regardless of how you
  produced or gathered the underlying research (manually, with a tool, any workflow) — the
  mechanism is immaterial; what matters is the wording that ends up in the file. Keep any raw
  research notes or extracts entirely outside this repository — they are never committed, under
  any filename. **A PR whose content isn't in the contributor's own words will not be merged** —
  it will be closed or sent back with a request to rewrite it, no matter how accurate or
  well-sourced the underlying facts are.
- Write `procedure.md` and `endpoints.md` in your own words (see rule 6 on capability language) —
  every `procedure.md` in this repo must meet that standard. If you want to preserve a citation
  trail, cite the source (doc title, URL, version, retrieval date) inline in `procedure.md` or
  `endpoints.md` — a citation is fine; quoting the vendor's actual text is not.
- Do not invent steps, thresholds, or verification methods the source doesn't state. If the source
  is silent on something an executor would need, say so as an explicit unknown in the procedure
  ("consult platform-specific guidance") rather than fabricating a plausible-sounding value.
- If research surfaces a defect in the vendor's own source documentation (a copy-pasted section
  that doesn't apply, an inconsistent worked example) — flag it and follow the source's stated
  rule instead of reproducing the defect. This is expected to happen as more vendors are added; it
  is not a sign something went wrong.

### 2. `SKILL.md` frontmatter and shape

Every `SKILL.md` uses the open [Agent Skills](https://agentskills.io/specification) format:

- **Frontmatter is `name` + `description` only** (+ `argument-hint` if the skill takes positional
  arguments worth hinting). Do not add a structured `interface`/`inputs`/`outputs`/`risk_level`
  block — that information belongs as prose in the body (see below), not as YAML schema.
- `description` must state: what the skill does, when to use it, which platforms/vendors it
  covers, and any tool/client assumption in plain language ("Assumes an authenticated API client
  for the target platform is already available"). Be specific enough that an agent's
  skill-matching picks this up for the right requests and not adjacent ones.
- Body sections, in order: title + one-line purpose, "When to use this skill," "Universal concepts"
  (only the facts that are genuinely true across every vendor listed — see rule 4), "Procedure"
  (routes to `references/<vendor>/procedure.md` — see rule 3), "Known limitations," "Contributing
  to this skill" (the stability rule — see rule 5), "Tools," "Verification checklist."

### 3. Multi-vendor skills route, they don't inline

If a capability covers more than one vendor (essentially always, per this repo's own
organizing principle), `SKILL.md` must stay a **thin, stable router**:

- The real per-vendor procedure — operational steps, error handling, patterns, known limitations,
  tools, verification checklist — lives entirely in `references/<vendor>/procedure.md`.
- `SKILL.md`'s own "Procedure" section does three things only: identify the target vendor/platform
  from the request, point to that vendor's `procedure.md` and say to follow it exactly, and report
  the outcome using that procedure's own status vocabulary (don't normalize it away).
- A `SKILL.md` whose length scales with the number of vendors it covers has the wrong shape. It
  should stay roughly constant size no matter how many vendors are added underneath it.

For a large permutation space where one vendor spans several platform families or install modes
(e.g. `software_upgrade_lifecycle`), the same shape holds one level deeper:
`references/<vendor>/<platform-family>.md`, grouped by platform family/version line — not by every
individual model — unless a specific model's behavior is genuinely, documentedly different from
its family. See `skills/software_upgrade_lifecycle/references/INDEX.md`'s "Grouping rule" section
for a worked example of applying (and validating) this.

### 4. "Universal concepts" must be genuinely universal — earn that section, don't assume it

`SKILL.md`'s "Universal concepts" section (or `references/<rfc-or-standard>-extract.md`, where one
exists) is the highest-scrutiny part of any skill in this repo, because it's the one section every
vendor addition can silently invalidate. When adding a new vendor:

- Check every claim currently in that section against the new vendor's actual behavior. Do not
  assume it holds; verify it.
- If the new vendor contradicts a "universal" claim, **that is a bug in `SKILL.md`, not a footnote
  in the vendor's own file** — correct the shared section, and say so explicitly in the PR
  description and (for anything non-trivial) in `references/INDEX.md`'s own notes. This has
  already happened multiple times in this repo (Freshservice invalidating a change-management
  approval assumption; PowerDNS invalidating a DNSSEC-scope assumption) — it's a normal, expected
  part of adding vendor #2 or later to any category, not a sign of a bad first draft.
- If a capability has no wire-level standard grounding it (true of most categories except DNS,
  which has RFC 1035/2136/1123) — say so plainly in `SKILL.md` rather than implying a
  standardization that doesn't exist, and hedge shared claims accordingly ("where the platform
  provides one," "where documented") rather than stating them as guaranteed.

### 5. `SKILL.md` stability rule (the actual point of this structure)

**`SKILL.md` only changes for two reasons: fixing a bug in its own shared/universal-concepts or
router content, or adding a pointer to a newly supported vendor.** Every vendor-specific fact — a
new error code, a corrected field name, a changed API path, an entirely new procedure — belongs in
that vendor's own `references/<vendor>/` files, never in `SKILL.md` itself.

A PR that adds or corrects one vendor's behavior should almost never touch `SKILL.md`. If it does,
the PR description must say explicitly which of the two allowed reasons applies.

### 6. Capability language, not literal wiring, in `SKILL.md` and `procedure.md`

- **REST/API-driven procedures:** describe operations generically ("update the change request
  record, setting the fields below") — never a literal HTTP method+path or SDK call in `SKILL.md`
  or the vendor's `procedure.md` prose. The literal route/method/field table belongs in
  `references/<vendor>/endpoints.md` as the wiring layer a human or orchestration layer consults
  to bind a step to a concrete tool.
- **CLI-driven procedures** (device/OS command syntax): the literal command IS the payload passed
  to a generic "run a command on this device" capability — there's no per-command tool to match
  against. Keep CLI syntax inline in the procedure; it does not need `endpoints.md` extraction.
- The exact expected output/state that confirms success belongs inline, in plain language, in both
  cases — that's what the agent checks regardless of which concrete tool executed the call.
- Every documented error condition gets plain-language handling inline; if the source gives an
  exact error string worth pattern-matching on, put the literal string in `endpoints.md` and refer
  to it by name from the procedure step.

### 7. Confirmation gates on anything destructive or hard to reverse

Any step that deletes, overwrites, reloads, or otherwise affects a live/production system must
have its confirmation gate written directly into that step — "stop and confirm with the operator
before proceeding" — never left for a calling agent to infer or remember to add. This is already
consistent practice across every skill in this repo (DNS deletes, device reloads, change-request
closes); keep it that way.

### 8. Naming — avoid silent collisions in `references/<vendor>/`

Once a vendor has more than one platform-family or mode file (not just once a *second* one is
added — from the first file), name every companion file after its procedure file's own basename:
`<platform-family-and-mode>-endpoints.md` — not a bare `endpoints.md`. A generic name silently
overwrites the previous file of the same name the next time a second platform-family is added for
that vendor. See `skills/software_upgrade_lifecycle/references/INDEX.md`'s "Lesson learned" note
for a worked example of applying this rule. The same collision risk applies to your own local
research notes if you keep more than one platform-family's worth in the same place.

### 9. Token budget discipline

`SKILL.md` loads on every invocation — keep it lean (the open spec's own guidance: under ~5000
tokens, under ~500 lines):

- No JSON example blocks or full field dictionaries in `SKILL.md` — those belong in `references/`.
- State a fact once. Don't restate a limitation in both "Known limitations" and inline — refer back
  to it ("see Known limitations") instead.
- Before considering a `SKILL.md` draft done, run `wc -w SKILL.md` (divide by ~0.75 for a rough
  token estimate) or `wc -c SKILL.md` (divide by ~4). If it's near or over the budget, move content
  to `references/` — don't just write more tersely in place.

### 10. Security and safety in skill content

- Never include real credentials, tokens, hostnames, or customer-identifying information in any
  `source-extract.md`, `procedure.md`, or example payload — use placeholder values throughout.
- Any vendor-documented step that is destructive or hard to reverse must be flagged as such in the
  procedure, not presented as routine (see rule 7).
- If you find a vendor's documented behavior itself is unsafe or exposes secrets (e.g. an endpoint
  that echoes credentials in a response, a default that disables a safety check), document that
  finding explicitly in the vendor's `procedure.md` rather than silently working around it. See
  [SECURITY.md](SECURITY.md) for how to report anything found after the fact in merged content.

## Review checklist

Before opening a PR, or before approving one, confirm:

- [ ] Every fact is grounded in the vendor's own current approved documentation (or a flagged,
      justified secondary source) — verified against that source, not assumed.
- [ ] No vendor documentation text is quoted or closely mirrored anywhere in the diff —
      `procedure.md` and `endpoints.md` restate every fact in the contributor's own original
      wording. Any PR that fails this must be sent back for rewrite, not merged (rule 1).
- [ ] `SKILL.md` was touched only to fix a shared/universal-concepts bug or add a vendor pointer —
      not to carry vendor-specific detail (rule 5).
- [ ] Any change to "Universal concepts" is called out explicitly as a correction, with the vendor
      finding that prompted it named (rule 4).
- [ ] No literal HTTP method+path appears in `SKILL.md` or a REST-driven `procedure.md`'s prose —
      it's in `endpoints.md` instead. CLI-driven procedures may keep literal commands inline.
- [ ] Every destructive or hard-to-reverse step has its confirmation gate written directly into
      that step.
- [ ] New companion files in a multi-file vendor directory follow the platform-family-prefixed
      naming convention (rule 8) — no generic `endpoints.md` that could collide with a future
      addition.
- [ ] `references/INDEX.md` for the affected skill lists the new/changed entry.
- [ ] `SKILL.md` is comfortably under the token/line budget (rule 9) — checked with `wc`, not
      eyeballed.
- [ ] No real credentials, tokens, or customer-identifying values anywhere in the diff.
- [ ] Nothing in the diff is business-specific, customer-specific, or judgment-heavy in a way that
      would fail the "two different engineers at two different companies" test.

See [CONTRIBUTING.md](CONTRIBUTING.md) for the process (branching, PR labels, CLA) around getting
a change like this reviewed and merged.
