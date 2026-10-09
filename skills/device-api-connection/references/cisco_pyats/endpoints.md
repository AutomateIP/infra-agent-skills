# Cisco pyATS — DNAC REST Bindings

Sources (Cisco rolling documentation; retrieved 2026-10-09):

- [REST Connector Introduction](https://pubhub.devnetcloud.com/media/rest-connector/docs/user_guide/introduction.html).
- [DNAC Services](https://pubhub.devnetcloud.com/media/rest-connector/docs/user_guide/services/dnac.html).
- [Generic Services](https://pubhub.devnetcloud.com/media/rest-connector/docs/user_guide/services/generic.html).

## Load connection

```yaml
devices:
  controller:
    os: dnac
    type: controller
    custom:
      abstraction:
        order: [os]
    connections:
      rest:
        class: rest.connector.Rest
        ip: "%ENV{DNAC_ADDRESS}"
        port: 443
        verify: true
        credentials:
          rest:
            username: "%ENV{DNAC_USERNAME}"
            password: "%ENV{DNAC_PASSWORD}"
```

The required `os` follows the introduction's selection rule. TLS verification is explicitly
set rather than copying the DNAC page's disabled-verification example.

## Establish session → Read interfaces → Disconnect

```python
from pyats.topology import loader
testbed = loader.load('rest-testbed.yaml')
device = testbed.devices['controller']
device.connect(via='rest')
try:
    response = device.rest.get('/dna/intent/api/v1/interface')
finally:
    device.rest.disconnect()
```

| Operation | Binding / documented argument |
|---|---|
| Establish session | `device.connect(via='rest')`; DNAC connection timeout accepts seconds |
| Read interfaces | `GET /dna/intent/api/v1/interface`; `api_url` required, optional `timeout` (documented default 30 seconds) |
| Disconnect | `device.rest.disconnect()` |

Authentication wire endpoints, token refresh, pagination, and response schemas are not specified
by the connector pages; none are inferred from other platforms.
