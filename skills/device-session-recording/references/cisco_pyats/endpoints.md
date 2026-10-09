# Cisco Unicon — Mock Session Binding

Sources (Cisco rolling documentation; retrieved 2026-10-09):

- [Playback](https://pubhub.devnetcloud.com/media/unicon/docs/playback/index.html).

## Local mock process in a testbed

The documented connection uses Unicon to spawn a mock command, rather than declaring the mock
process itself as a pyATS connection class. The OS and `connect` state must exist in the data.

```yaml
devices:
  dut:
    os: iosxe
    type: router
    connections:
      defaults:
        class: unicon.Unicon
      cli:
        command: mock_device_cli --os iosxe --mock_data_dir mock_data --state connect
        protocol: unknown
```

```python
from pyats.topology import loader
testbed = loader.load('mock-testbed.yaml')
device = testbed.devices['dut']
device.connect(via='cli')
```

The mock process needs all commands sent during connection initialization as well as the actual
test commands. This binding does not contain a live device address.
