# Cisco pyATS REST Connector — DNAC Read Session

Connects to DNAC, reads the documented interface resource, and disconnects.

Sources (Cisco rolling documentation; retrieved 2026-10-09):

- [REST Connector Introduction](https://pubhub.devnetcloud.com/media/rest-connector/docs/user_guide/introduction.html).
- [DNAC Services](https://pubhub.devnetcloud.com/media/rest-connector/docs/user_guide/services/dnac.html).
- [Generic Services](https://pubhub.devnetcloud.com/media/rest-connector/docs/user_guide/services/generic.html).

## 1. Define the DNAC connection

The connector uses the device OS and abstraction order to select a supported implementation.
The DNAC connection contains the REST class, controller address, and its credential block.
[endpoints.md](endpoints.md) supplies the exact definition. Success is a loaded DNAC device object.
The introduction requires an OS selector even though the DNAC page's example omits it; this
procedure follows the stated requirement rather than reproducing that omission.

## 2. Establish the session

The connection operation authenticates using the supplied connection credentials. DNAC documents
port 443 when no port is specified. Success is a completed connection; authentication, TLS, or
transport failure leaves the read sequence incomplete. The connector guide does not enumerate
error strings or token-refresh guarantees; those details remain unverified, and a failing session
requires inspection of its actual error before any new attempt.

## 3. Read interfaces

The documented interface-read operation returns the controller response through the REST session.
Success is a successful controller response, not merely the existence of a session. The connector
page does not specify the response schema or a complete HTTP-error contract; the raw response/status
remains evidence, and resource-specific interpretation requires the matching DNAC API reference.
No count, pagination completeness, or health conclusion is inferred here.

## 4. Disconnect

The generic disconnect operation ends the REST session. Success is completed disconnection;
errors are reported alongside the read result rather than replacing it.

## Patterns

- A CLI session and a REST session are different connections, even for the same device.
- The abstraction-enabled REST class selects supported implementations; it is not a documented
  universal connector for every HTTP service.

## Known limitations

- Cisco's DNAC example sets TLS verification off. That disables certificate checking; the example
  here explicitly enables it instead. Certificate errors require the correct trust setup, not an
  automatic downgrade.
- No mutation, custom connector implementation, automatic pagination, or reauthentication loop.
- Quotas, retry intervals, and exception types are unverified in the cited connector guide.

## Tools

| Operation | Description | Category |
|---|---|---|
| Load connection | Load the DNAC testbed definition | Local Python |
| Establish session | Connect using the DNAC REST implementation | Authentication |
| Read interfaces | Return the documented controller resource response | Read |
| Disconnect | Close the REST session | Connection |

## Verification checklist

- [ ] DNAC OS selection, abstraction order, and connection credentials are present.
- [ ] TLS verification was enabled.
- [ ] Connection and interface-read outcomes are separately accounted for.
- [ ] Response interpretation is limited to verified controller documentation.
- [ ] Session disconnection completed or its error was reported.
