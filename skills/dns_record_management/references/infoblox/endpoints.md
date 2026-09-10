# Endpoints — Infoblox NIOS WAPI

Literal wiring layer for each capability named in `SKILL.md`. Object types and payload field names
are taken directly from the source Itential adapter task definitions. The REST method/path forms
follow Infoblox's standard WAPI convention (`POST` to create, `GET` with a query for lookup) —
**inferred**, not copied from a WAPI reference doc; confirm against the target Grid Master's WAPI
version before use.

## A Record

- **Create A Record** — `POST /wapi/v2.x/record:a`
  Body: `{"name": "<hostname>", "ipv4addr": "<ip_address>", "view": "<dns_view>"}`
- **Confirm A Record** — `GET /wapi/v2.x/record:a?name=<hostname>&ipv4addr=<ip_address>&view=<dns_view>&_return_fields=name,ipv4addr`

## CNAME Record

- **Create CNAME Record** — `POST /wapi/v2.x/record:cname`
  Body: `{"name": "<alias_name>", "canonical": "<canonical_name>", "view": "<dns_view>"}`
- **Confirm CNAME Record** — `GET /wapi/v2.x/record:cname?name=<alias_name>&canonical=<canonical_name>&view=<dns_view>&_return_fields=name,canonical`

## PTR Record

- **Create PTR Record** — `POST /wapi/v2.x/record:ptr`
  Body: `{"ptrdname": "<hostname>", "ipv4addr": "<ip_address>", "view": "<dns_view>"}`
- **Confirm PTR Record** — `GET /wapi/v2.x/record:ptr?ptrdname=<hostname>&ipv4addr=<ip_address>&view=<dns_view>&_return_fields=name,ptrdname,ipv4addr`

## NS Record

- **Create NS Record** — `POST /wapi/v2.x/record:ns`
  Body: `{"name": "<ns_record>", "nameserver": "<name_server>", "addresses": <addresses>, "view": "<dns_view>"}`
- **Confirm NS Record** — `GET /wapi/v2.x/record:ns?name=<ns_record>&nameserver=<name_server>&view=<dns_view>&_return_fields=name,nameserver`

## Fixed Address Record

- **Create Fixed Address Record** — `POST /wapi/v2.x/fixedaddress`
  Body: `{"ipv4addr": "<ip_address>", "mac": "<mac_address>"}`
- **Confirm Fixed Address Record** — `GET /wapi/v2.x/fixedaddress?ipv4addr=<ip_address>&mac=<mac_address>&_return_fields=ipv4addr,mac`

## Error handling

The source project does not carry forward Infoblox's literal duplicate-object error string — it
routes any non-empty query response (`response.result` count > 0, evaluated before create in the
source workflows) into an "already exists" branch, and any adapter-level failure into a generic
"Infoblox Error" surface (`$var.job.infobloxError`). Treat these as:

- **Pre-existing object**: if a lookup by the same key fields returns 1+ results before create is
  attempted, report `ALREADY_EXISTS` without calling create.
- **Any other create/read failure**: report `FAILED` with the adapter/API error body verbatim — the
  source does not specify a narrower taxonomy of NIOS WAPI error codes to pattern-match on.
