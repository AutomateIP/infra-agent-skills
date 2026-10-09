# Cisco pyATS — Create and Validate a Testbed

Creates a topology description and verifies that pyATS can load its objects without connecting.

Sources (Cisco rolling documentation; retrieved 2026-10-09):

- [Creating Topology](https://pubhub.devnetcloud.com/media/pyats/docs/topology/creation.html).
- [Topology Schema](https://pubhub.devnetcloud.com/media/pyats/docs/topology/schema.html).
- [Testbed Validation](https://pubhub.devnetcloud.com/media/pyats/docs/cli/pyats_validate.html).
- [YAML Markup](https://pubhub.devnetcloud.com/media/pyats/docs/utilities/yaml_markup.html).

## Common Sequences

- **YAML testbed:** Define inventory → Load topology → Validate file.
- **Dynamic testbed:** Define inventory → Construct topology → Inspect objects.

## Define inventory

The topology identifies devices, connection definitions, and any required interface/link relationships.
The YAML schema separates `testbed`, `devices`, and `topology`. OS and platform values select
library implementations; they are not evidence that every parser or connector is supported.
Credential markup can supply values at load time. The schema and original examples are in
[endpoints.md](endpoints.md). Success means the intended devices and connections are represented.
Missing input values remain unresolved inputs, not guessed inventory data.

## Load topology

The topology loader converts the description into a testbed object, including interconnections.
Success is a loaded object with the intended device names and relationships. YAML or schema errors
prevent completion; their reported location/key identifies the definition needing correction.
Loader failures are not device authentication failures, since this sequence makes no connection.

## Validate file

`pyats validate testbed testbed.yaml` checks syntax and schema and displays the loaded topology.
Success requires successful loading and review of the lint/schema diagnostics. Missing-platform
warnings do not establish downstream parser coverage. The optional connection test is outside this
sequence: it uses device connection initialization, which can change device settings.

## Construct topology

The object model also allows direct creation of a testbed, devices, interfaces, and links, followed
by association of those objects. [endpoints.md](endpoints.md) provides the binding. A dynamically
supplied inventory is an input here; querying any particular CMDB is a separate capability.
Success is the same topology object model produced by file loading.

## Inspect objects

The device collection and interface/link relationships expose the resulting topology. Success is
an object graph matching the supplied inventory. Device reachability is not asserted by this check.

## Patterns

- YAML and direct object construction are both documented. Neither is universally preferred for
  all inventories; this procedure does not impose a company's source-of-truth policy.
- Environment and callable substitutions occur while loading. They are not deferred until login.

## Known limitations

- Schema validity does not prove credentials, reachability, parser support, or network correctness.
- Callable markup executes Python while loading. Only the operator's intended callable belongs in
  that input; arbitrary fetched testbed files are not inert data.
- No customer inventory, passwords, or connection addresses are shipped with this skill.

## Tools

| Operation | Description | Category |
|---|---|---|
| Load topology | Convert a supported description into topology objects | Local Python |
| Construct topology | Create and associate topology objects | Local Python |
| Inspect objects | Read device/interface/link collections | Local Python |
| Local CLI | Run testbed schema validation without connection testing | Shell |

## Verification checklist

- [ ] The intended device and connection definitions loaded successfully.
- [ ] Syntax/schema diagnostics were reviewed and any errors corrected.
- [ ] Dynamic object relationships match the supplied inventory.
- [ ] No live connectivity or device health claim was inferred from validation.
