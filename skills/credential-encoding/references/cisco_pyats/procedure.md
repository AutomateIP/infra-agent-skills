# Cisco pyATS — Encode and Load Credentials

Applies Cisco's documented Fernet secret-string setup and loads credential markup.

Sources (Cisco rolling documentation; retrieved 2026-10-09):

- [Secret Strings](https://pubhub.devnetcloud.com/media/pyats/docs/utilities/secret_strings.html).
- [YAML Markup](https://pubhub.devnetcloud.com/media/pyats/docs/utilities/yaml_markup.html).

## Common Sequences

- **Encrypted value in a testbed:** Configure representer → Encode value → Verify decoding → Load credential.
- **Runtime-supplied value:** Select runtime markup → Load credential.

## Configure representer

Cisco's secure setup selects the Fernet representer in the pyATS configuration, installs
`cryptography`, restricts the configuration permissions with `chmod 600 ~/.pyats/pyats.conf`,
then generates a key with `pyats secret keygen` and stores it in that configuration.
The configuration keys are in [endpoints.md](endpoints.md).
**Before replacing an existing representer or key: stop and confirm with the operator before
proceeding.** Existing encoded values depend on their original key/representer. Success means the
selected representer and its generated key are configured together, not merely that keygen ran.

## Encode value

`pyats secret encode` obtains the secret through its prompt and returns an encoded value.
A named representer uses `pyats secret encode --prefix example` with the matching configuration.
Success is an encoded value from the selected representer. Missing cryptography support or a
missing required key prevents secure encoding; the failure remains unresolved until corrected.

## Verify decoding

Cisco's verification uses `pyats secret decode '<encoded-value>'` with the same configuration.
Success is recovery of the original value. **This command exposes plaintext on stdout**; the
verification belongs in the operator's private terminal, and its output must not be copied to a
report or shared log. A decoding error is not a reason to switch to the obscuring representer.

## Select runtime markup

The documented alternatives substitute an environment variable, prompt for a value, or call a
Python function during YAML loading. The exact forms are in [endpoints.md](endpoints.md).
Success means the chosen input channel is available at load time. Interactive prompting requires
an attended run; absent runtime input leaves the credential unresolved. The guide does not define
a universal missing-variable error contract.

## Load credential

The loader resolves the markup in the credential password field. Encoded passwords use the
configured representer; the resulting secret-string representation masks ordinary display.
Success is a loaded credential; recovering plaintext for authentication is a separate point of use.
The documented callable is executed during loading, so its implementation and inputs must already
belong to the requested workflow.

## Patterns

- A representer prefix ties a value to the corresponding key/configuration entry.
- Masked string display does not mean the in-memory value cannot be decoded.

## Known limitations

- **The default obscuring representer is not cryptographically secure.** `%ENC{}` alone does not
  establish encryption; Cisco requires the explicit Fernet setup for that property.
- The plaintext representer stores plaintext. The decode CLI and plaintext accessor reveal secrets.
- Secret-store integration code and device password rotation are outside this procedure.

## Tools

| Operation | Description | Category |
|---|---|---|
| Local CLI | Configure file permissions and run the secret-string commands | Shell |
| Load credential | Resolve testbed markup using the configured representer | Local Python |

## Verification checklist

- [ ] Fernet and its key were selected for encrypted storage.
- [ ] The matching key/representer recovered the value in a private verification.
- [ ] Credential markup loads successfully without sharing its plaintext.
- [ ] No encoded value was described as secure solely because it uses `%ENC{}`.
