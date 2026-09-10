# Infoblox NIOS — DNS/DHCP Record Creation Procedure

Create DNS/DHCP records in Infoblox NIOS DDI — A, CNAME, PTR, NS, and Fixed Address records — via the
NIOS WAPI, including the pre-create existence check and post-create confirmation each record type uses.
Create-only; does not cover modify, delete, or network/network-container creation. Assumes an
authenticated WAPI client against a NIOS Grid Master (DNS/DHCP record create and read permissions on the
target view/network) is already available.

Source note: derived from an Itential-built Infoblox NIOS DDI workflow project (adapter task
definitions), not directly from Infoblox's published WAPI reference manual. The per-field payload shapes
are taken directly from that project. The literal WAPI REST paths in `endpoints.md` are inferred from
Infoblox's standard WAPI object-type conventions (`record:a`, `record:cname`, `record:ptr`, `record:ns`,
`fixedaddress`) — verify against the target Grid Master's actual WAPI version/schema before relying on
them unmodified.

## Operational procedure

### Common Sequences

Each record type follows the same three-step shape — build the payload, create the record, confirm it.
There is no cross-record-type sequencing; pick the one matching the requested record type and run it:

1. **Create an A Record** → Build A Record Payload → Create A Record → Confirm A Record
2. **Create a CNAME Record** → Build CNAME Payload → Create CNAME Record → Confirm CNAME Record
3. **Create a PTR Record** → Build PTR Payload → Create PTR Record → Confirm PTR Record
4. **Create an NS Record** → Build NS Record Payload → Create NS Record → Confirm NS Record
5. **Create a Fixed Address Record** → Build Fixed Address Payload → Create Fixed Address Record →
   Confirm Fixed Address Record

### Create an A Record

Requires a hostname, an IPv4 address, and a DNS view.

1. **Build A Record Payload** — assemble an object with `name` = hostname, `ipv4addr` = address, `view`
   = the DNS view.
2. **Create A Record** — submit the payload as a new `record:a` object. See `endpoints.md` for the
   literal call.
3. **Confirm A Record** — read back `record:a` objects matching the same `name`/`ipv4addr`/`view`,
   requesting fields `name,ipv4addr`. If a match is returned, report CREATED with the returned record.
   If the create call reports the object already exists, report ALREADY_EXISTS (see the documented
   duplicate-object error in `endpoints.md`) rather than treating it as a failure.

### Create a CNAME Record

Requires an alias (source) name, a canonical (target) name, and a DNS view.

1. **Build CNAME Payload** — assemble an object with `name` = the alias name, `canonical` = the
   canonical name, `view` = the DNS view.
2. **Create CNAME Record** — submit the payload as a new `record:cname` object.
3. **Confirm CNAME Record** — read back `record:cname` objects matching the payload, requesting fields
   `name,canonical`. Report CREATED with the returned record, or ALREADY_EXISTS on the documented
   duplicate-object error.

### Create a PTR Record

Requires a hostname, an IPv4 address, and a DNS view.

1. **Build PTR Payload** — assemble an object with `ptrdname` = hostname, `ipv4addr` = address, `view` =
   the DNS view.
2. **Create PTR Record** — submit the payload as a new `record:ptr` object.
3. **Confirm PTR Record** — read back `record:ptr` objects matching the payload, requesting fields
   `name,ptrdname,ipv4addr`. Report CREATED with the returned record, or ALREADY_EXISTS on the documented
   duplicate-object error.

### Create an NS Record

Requires the delegated domain name, the authoritative name server, its IP address(es), and a DNS view.

1. **Build NS Record Payload** — assemble an object with `name` = the delegated domain, `nameserver` =
   the authoritative name server, `addresses` = its IP address(es), `view` = the DNS view.
2. **Create NS Record** — submit the payload as a new `record:ns` object.
3. **Confirm NS Record** — read back `record:ns` objects matching `name`/`nameserver`/`view`, requesting
   fields `name,nameserver`. Report CREATED with the returned record, or ALREADY_EXISTS on the documented
   duplicate-object error.

### Create a Fixed Address Record

Requires an IPv4 address and a MAC address. No DNS view — fixed addresses are DHCP-scoped, not
DNS-view-scoped.

1. **Build Fixed Address Payload** — assemble an object with `ipv4addr` = the address, `mac` = the MAC
   address.
2. **Create Fixed Address Record** — submit the payload as a new `fixedaddress` object.
3. **Confirm Fixed Address Record** — read back `fixedaddress` objects matching `ipv4addr`/`mac`,
   requesting fields `ipv4addr,mac`. Report CREATED with the returned record, or ALREADY_EXISTS on the
   documented duplicate-object error.

## Patterns

- **Idempotent by design.** Every path checks for an existing match before treating a duplicate as an
  error — ALREADY_EXISTS is a normal outcome, not a failure.
- **Fixed Address is the odd one out.** It's the only record type here with no DNS view — it's
  DHCP-scoped, not DNS-scoped, so don't try to pass a view for it.

## Known limitations

- Create-only; does not cover modify, delete, or network/network-container creation.
- Field names and WAPI paths here are derived from an internal Itential workflow project, not directly
  from Infoblox's published WAPI reference manual — verify against the target Grid Master's actual
  schema before relying on this unmodified in production.
- Reversible by deleting the created object via its WAPI reference (`_ref`) — not covered by this skill.

## Tools

| Operation | Description | Category |
|---|---|---|
| `record:a` create/read | Create and confirm an A record | DNS Records |
| `record:cname` create/read | Create and confirm a CNAME record | DNS Records |
| `record:ptr` create/read | Create and confirm a PTR record | DNS Records |
| `record:ns` create/read | Create and confirm an NS record | DNS Records |
| `fixedaddress` create/read | Create and confirm a DHCP Fixed Address record | DHCP Records |

## Verification checklist

- [ ] Record type's required fields were all supplied before building the payload
- [ ] A follow-up read confirmed the record (or the documented duplicate-object error was correctly
      reported as ALREADY_EXISTS, not FAILED)
- [ ] DNS view supplied for a/cname/ns/ptr; correctly omitted for fixedaddress
