# Cisco pyATS — Credential Representation Bindings

Sources (Cisco rolling documentation; retrieved 2026-10-09):

- [Secret Strings](https://pubhub.devnetcloud.com/media/pyats/docs/utilities/secret_strings.html).
- [YAML Markup](https://pubhub.devnetcloud.com/media/pyats/docs/utilities/yaml_markup.html).

## Representer configuration

```ini
[secrets]
string.representer = pyats.utils.secret_strings.FernetSecretStringRepresenter
string.key = <operator-generated-key>
```

An additional representer uses `example.representer` and `example.key` under `[secrets]`.
Key generation, encoding, and decoding accept `--prefix example`.

## Credential markup

| Form | Load-time behavior |
|---|---|
| `"%ENV{PYATS_PASSWORD}"` | Resolves the named environment variable; missing-input error details are not specified in the markup guide |
| `"%ASK{Device password}"` | Prompts during loading |
| `"%ENC{<encoded-value>}"` | Decodes through the default configured representer |
| `"%ENC{<encoded-value>, prefix=example}"` | Selects the named representer |
| `"%CALLABLE{package.module.function()}"` | Invokes the supplied importable callable |

These are YAML scalar values, not complete testbeds. Values beginning with `%` are quoted.

## Load credential and plaintext access

```python
from pyats.topology import loader
from pyats.utils.secret_strings import SecretString, to_plaintext

testbed = loader.load('testbed.yaml')
secret = testbed.credentials.default.password
```

`str(secret)` masks a `SecretString`; `secret.plaintext` and `to_plaintext(secret)` expose its
plaintext. `SecretString.from_plaintext(value)` creates an encoded secret-string object using
the configured representer. None of these accessors is a secret-manager retrieval API.
