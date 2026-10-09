# Cisco pyATS — Topology Bindings

Sources (Cisco rolling documentation; retrieved 2026-10-09):

- [Creating Topology](https://pubhub.devnetcloud.com/media/pyats/docs/topology/creation.html).
- [Topology Schema](https://pubhub.devnetcloud.com/media/pyats/docs/topology/schema.html).
- [YAML Markup](https://pubhub.devnetcloud.com/media/pyats/docs/utilities/yaml_markup.html).

## YAML definition

Original minimal example; the environment supplies the actual values. `dut` is a placeholder device
identifier. The `iosxe` OS below is an example selection, not a universal default.

```yaml
testbed:
  name: example_testbed
  credentials:
    default:
      username: "%ENV{PYATS_USERNAME}"
      password: "%ENV{PYATS_PASSWORD}"
devices:
  dut:
    os: iosxe
    type: router
    connections:
      cli:
        protocol: ssh
        ip: "%ENV{PYATS_DEVICE_ADDRESS}"
```

## Load topology

```python
from pyats.topology import loader
testbed = loader.load('testbed.yaml')
```

A caller-supplied inventory mapping can also be loaded without writing YAML:

```python
from genie.testbed import load
testbed = load(inventory_mapping)
```

`inventory_mapping` follows the testbed schema. Source: Cisco's
[Genie Cookbook, dictionary testbeds](https://pubhub.devnetcloud.com/media/genie-docs/docs/cookbooks/genie.html),
rolling documentation retrieved 2026-10-09. Fetching the inventory remains outside this operation.

## Construct topology

The following uses the documented object-association mechanism. `connection_definition` is an
input mapping of connection names to their platform-specific settings.

```python
from pyats.topology import Testbed, Device, Interface, Link

testbed = Testbed('example_testbed')
device = Device('dut', os='iosxe', type='router', connections=connection_definition)
device.testbed = testbed
interface = Interface('GigabitEthernet1', type='ethernet')
device.add_interface(interface)
interface.link = Link('example_link')
```

## Inspect objects

`testbed.devices`, `device.interfaces`, and the interface's `link` expose the constructed graph.
A loader exception is preserved with its relevant schema/key context; this reference does not
assign one universal exception string to all malformed descriptions.
