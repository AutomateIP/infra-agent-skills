# Cisco ASA — Route-Based IPsec VPN with Virtual Tunnel Interfaces (Static and Dynamic VTI)

Creates a route-based IPsec VPN on a Cisco ASA using a Virtual Tunnel Interface: either a static VTI
(an always-on tunnel between two fixed peers) or a dynamic VTI (a hub that spawns a per-session
virtual access interface from a template, for hub-and-spoke). Covers the building blocks in order:
IPsec proposal, IPsec profile, then the tunnel interface or virtual template. Literal commands live
in `endpoints.md`, under headings that match the bold operation names below.

Scope: create/configure only. The source chapter documents no read/inspect, update, or delete
procedure and no verification commands, so those are out of scope here and stated as unknown rather
than filled in. Also out of scope: the peer device's own configuration (the chapter's IOS peer
example is not reproduced), dynamic-routing protocol configuration, IP SLA/route tracking, and
policy-based (crypto map) VPN. Assumes CLI access to the ASA in privileged/configuration mode, direct
or through an existing remote-execution capability.

Source: Cisco, "Virtual Tunnel Interface" chapter of the ASA configuration guide (PDF supplied by the
contributor, 14 pages including feature history through ASA 9.19(1)), read 2026-10-06. No secondary
sources were used. The chapter itself does not print its guide version/date.

## Before you start (applicability, from the chapter's guidelines)

- ASA must be in single context mode, routed firewall mode, and not clustered. Dynamic VTI additionally
  does not support ECMP, VRF, IKEv1, or QoS.
- Release gating from the feature history: dynamic VTI, OSPF/EIGRP over VTI, and loopback as tunnel
  source or addressing source require 9.19(1) or later; IPv6 on static VTI and the 1024-VTI limit
  require 9.16(1) or later (earlier limit was 100). The release in which static VTI first appeared is
  not in this chapter — unknown. Confirm the target ASA's release before choosing features.
- The VTI count limit is 1024 per device for static VTIs (and 1024 or the platform's interface limit,
  whichever is lower, for dynamic virtual access interfaces). Subinterfaces with a nameif count
  toward the total; port-channel member interfaces do not (only the port-channel itself). The usable
  count can be lower still: it cannot exceed the number of VLANs the platform supports.
- Pick the IKE version per tunnel; VTI supports IKEv1 and IKEv2 (dynamic VTI: IKEv2 only).
- Both tunnel endpoints and the tunnel itself can use IPv4 or IPv6 (any of v4-over-v4, v4-over-v6,
  v6-over-v4, v6-over-v6), but one VTI's tunnel mode is either IPv4 or IPv6, not both, and only
  static IPv6 addresses are accepted as tunnel source and destination.
- Do not use an address borrowed from another interface as the tunnel source address, for static or
  dynamic VTI.
- The peer must be configured with a matching IPsec proposal and IPsec profile parameters; SA
  negotiation begins once all tunnel parameters exist on the ASA.
- The chapter notes: on an ASA that sits in two VTI domains and has a BGP adjacency on a physical
  interface, an interface-health-check state change deletes the physical interface's routes until BGP
  re-establishes with the new active peer; this does not affect logical VTI interfaces.

## Operational procedure

1. **Decide the tunnel type and authentication.** Static VTI for a fixed site-to-site pair; dynamic
   VTI for a hub serving many or DHCP-addressed spokes (the hub supports only hub-and-spoke topology
   in this feature). Authentication is either a pre-shared key or certificates; IKEv2 permits
   different methods/keys on each side. For both IKE versions the pre-shared key is configured under
   the tunnel group used for the VTI (see step 8). Obtain the pre-shared key from the caller's own
   secret store; never embed a literal key in a script or log.

2. **Check for crypto-map coexistence on the device.** If the device runs dynamic crypto maps for
   LAN-to-LAN VPNs and also gets a dynamic VTI, only the dynamic VTI tunnels come up, because both
   compete for the default tunnel group. The source's recommended remedies are to migrate those VPNs
   to dynamic VTI or to give static crypto maps their own tunnel groups. A VTI and a crypto map can
   share one physical interface only if the crypto map's peer address differs from the VTI's tunnel
   destination. **Stop and confirm with the operator before adding a dynamic VTI to a device that
   carries existing crypto-map LAN-to-LAN tunnels — those tunnels may stop coming up.**

3. **Create the IPsec proposal** — an IKEv1 transform set (**Create IKEv1 transform set**) or an IKEv2
   IPsec proposal (**Create IKEv2 IPsec proposal**), choosing ESP encryption and integrity/
   authentication algorithms. Exact keyword lists are in `endpoints.md`. If a proposal with the chosen
   name already exists, the source does not say what re-entering it does; **stop and confirm with
   the operator before reusing an existing name**, since a live profile may reference it.

4. **Create the IPsec profile** (**Create IPsec profile**), then attach exactly one proposal to it:
   **Bind a proposal to the IPsec profile** (IKEv1 transform set or IKEv2 proposal). Then apply any
   of these optional settings as needed:
   - **Set SA lifetime** — time and/or traffic volume for the security association.
   - **Set responder-only** — makes this end never initiate the tunnel or rekeying. For IKEv2, the SA
     lifetime on the responder-only end should be longer than the lifetime in the initiator's
     profile so the initiator can rekey and the tunnel stays up. For IKEv1 with an IOS peer, the IOS
     side should be the responder-only one (IOS lacks continuous-channel mode), leaving the ASA as
     initiator. If the peer's rekey behavior is unknown, remove responder-only (making SA setup
     bidirectional) or give the responder-only end an unlimited IPsec lifetime to avoid expiry.
   - **Set PFS group** — unique session key per exchange; both peers must use the same DH group.
   - **Set trustpoint** — the certificate used when this end initiates a VTI tunnel.
   - **Set reverse route injection** — enables RRI for the profile, with the reverse route marked
     dynamic.

5. **Static VTI: create the tunnel interface** (**Create static VTI interface**): assign a tunnel
   ID and interface name, give it an address (explicit, or inherited via an unnumbered reference to a
   physical or loopback interface), set the tunnel source (physical or loopback interface) and the
   peer destination address, set the tunnel mode to IPsec IPv4 (IPv6 per the guidelines above),
   and bind the IPsec profile. Optionally restrict the tunnel with a traffic-selector access list
   (**Create static VTI interface**, optional selector); with none, the static VTI proposes
   any-to-any selectors. Note the ID-range inconsistency in Known limitations.

6. **Dynamic VTI: create the virtual template** (**Create dynamic VTI virtual template**). Prerequisites
   from the source: the IPsec profile and the interface to borrow an address from (an unnumbered
   source) already exist. The template must not be in shutdown. Mandatory parameters are the
   interface name, the tunnel IPsec mode, and the tunnel IPsec profile; borrowing an address from
   any physical or loopback interface is also configured, and all virtual access interfaces cloned
   from the template then share that address. The tunnel source interface is optional: if set, the
   ASA accepts VPN session requests only on that interface and cloned interfaces inherit its MTU; if
   omitted, requests are accepted on any interface, IKEv2 is enabled on all interfaces except
   management-only and failover ones, and the
   cloned interface inherits the MTU of whichever interface received the request. Virtual access
   interfaces are created per session and removed when the session ends.
   **Stop and confirm with the operator before modifying an existing virtual template — the source
   requires shutting the template down first, which drops sessions built from it.**

7. **Dynamic VTI: attach the template to a tunnel group** (**Attach virtual template to a tunnel
   group**). One template can be attached to several tunnel groups. Then enable dynamic route
   exchange (**Enable dynamic-VTI route exchange on a tunnel group**): the route-set option makes the
   ASA advertise its VTI address during IKEv2 so VTI-to-VTI unicast reachability exists for BGP or
   path monitoring; the accept option (on by default) lets it accept the peer's VTI address. After
   that, a routing policy to carry dynamic-VTI traffic and an access rule permitting the encrypted
   traffic are still required; the chapter gives no commands for either — unknown.

8. **Configure the IKE side.** The chapter's step-by-step sections stop at the tunnel interface; the
   IKEv2 policy, tunnel-group pre-shared-key (or trustpoint) settings, and enabling IKEv2 on the
   outside interface appear only in its worked static-VTI example (**IKE-side prerequisites** in
   `endpoints.md`). The tunnel-group name must match the identity the peer presents for IKEv1/IKEv2.
   For IKEv1 site-to-site tunnel groups, a name that is not an IP address is allowed only with
   certificate authentication and/or aggressive mode on the peer. Algorithm choices in the example
   are illustrative, not documented requirements.

9. **Handle access control** if traffic through the VTI needs it. Interface ACLs can be applied to a
   VTI. The VTI's security level is fixed at 0 and cannot be changed. Two global options exist:
   bypass ACL checking for IPsec traffic, and permit same-security-level intra-interface traffic
   (needed for an ACL on a level-0 VTI to be hit when the outside interface is also level 0)
   (**ACL-bypass and hairpin options**). **Stop and confirm with the operator before enabling either
   — both are device-wide changes that relax filtering, not scoped to this tunnel.**

10. **Check the peer.** If an IKEv2 peer sends configuration-request payloads, the ASA cannot
    establish the tunnel; the request must be disabled on the peer (**Peer-side note**).

11. **Persist the configuration.** The source chapter does not cover saving the running configuration
    — unknown; consult the platform's general configuration-management guidance before assuming the
    change survives a reload.

12. **Confirm the outcome** — see Verification.

## Changing a live VTI (documented disruptive actions)

- **MTU change.** A VTI's MTU follows its underlying physical interface automatically at the time the
  VTI is enabled. If the physical MTU is changed afterward, the VTI must be disabled and re-enabled to
  pick up the new value. **Stop and confirm with the operator before disabling the VTI — this drops
  the tunnel and its traffic until it is re-enabled.** The exact disable/enable commands are not
  printed in the chapter (assumed to be the interface `shutdown` / `no shutdown` pair).
- **BFD neighbor not coming up on a static VTI.** If the BFD neighbor fails because the interface has
  both IPv4 and IPv6 addresses, the source says to remove the tunnel interface and add it back with a
  single address type. **Stop and confirm with the operator before removing the tunnel interface —
  removal tears down the live tunnel.** For BFD, enable it only on the tunnel interface (static VTI) or
  on the tunnel group (dynamic VTI), never also as a BGP neighbor setting for the same overlay
  neighbor, and set the DPD interval to 60 seconds or more, scaling up with the number of BFD tunnels.

## Verification

The chapter provides no inspection or `show` commands, no expected output, and no error strings.
What it does state:

- The VTI supports ICMP ping between VTI interfaces; a ping between the two tunnel addresses is a
  reasonable reachability probe (the chapter does not itself prescribe it as a verification step —
  an inference).
- Dynamic VTI virtual access interfaces appear per session, named after the template's interface name
  with a `_va<n>` suffix.
- IKE and IPsec SAs are re-keyed continuously regardless of traffic, so an up tunnel is expected to
  stay up with no traffic.
- Commands to display IKE/IPsec SA state, VPN sessions, or the virtual access interfaces on the ASA
  are not given in this source — unknown. Consult platform-specific guidance for state inspection and
  do not report the tunnel as established from configuration entry alone.

## Known limitations

- Create/configure only; read, update, and delete are not documented in the source (see Scope).
- Source inconsistency: the tunnel-interface step gives an ID range of 0-10413 and says up to 10413
  VTIs are supported, but the chapter's guidelines and feature history state a 1024-per-device limit
  for static VTIs. This procedure follows the 1024 limit; confirm against the target release.
- Source defect: the static-VTI address step describes borrowed-address inheritance in the dynamic
  template's wording (copy-paste). Applied here as the same borrowed-address option for a static
  tunnel interface per the feature history; whether it is exclusive of an explicit address is unknown.
- Default values and valid ranges for lifetimes are not stated; accepted PFS groups beyond group 14
  are not stated; certificate-auth tunnel-group syntax is not stated.
- Unsupported or restricted per the chapter: GRE termination (VTI is IPsec mode only); clustering;
  multiple-context mode; transparent firewall mode; Security Group Tag frames/packets are dropped
  after VTI decryption; dynamic VTI with ECMP, VRF, clustering, IKEv1, or QoS.
- Not covered: IP SLA / static route tracking (the chapter recommends it so the tunnel stays up when
  the active-tunnel router is unavailable and points to the ASA general operations guide), the
  peer's configuration, and local tunnel ID/Umbrella SIG behavior from the feature history.
- Destructive/disruptive steps (each gated above): reusing an existing proposal name, adding a dynamic
  VTI beside crypto-map tunnels, modifying a virtual template, disabling a VTI for an MTU change,
  removing a tunnel interface, and enabling device-wide ACL-bypass or intra-interface options.

## Tools

An authenticated CLI/console session against the target ASA (SSH or console), in configuration mode.
The commands are payload sent through that session, not separate tools; see `endpoints.md`.

| Operation | Description | Category |
|---|---|---|
| Run a command on the ASA | Send configuration commands from `endpoints.md` over an authenticated CLI session | Execution/transport |

## Verification checklist

- [ ] Target ASA is single-context, routed-mode, non-clustered, and its release supports the chosen
      features (dynamic VTI/loopback source need 9.19(1)+; IPv6 static VTI needs 9.16(1)+)
- [ ] Crypto-map coexistence checked before adding a dynamic VTI, and operator confirmed
- [ ] IPsec proposal, IPsec profile (with proposal attached), and tunnel interface or virtual
      template + tunnel-group attachment all configured, with the peer using matching parameters
- [ ] Pre-shared key (if used) taken from the caller's secret store, not a literal in the procedure
- [ ] Tunnel source is not an address borrowed from another interface
- [ ] Any MTU-driven disable/re-enable, interface removal, template modification, or device-wide
      ACL-bypass option was confirmed with the operator first
- [ ] Tunnel state confirmed from the ASA's own SA/session state (command unknown in this source),
      not from configuration entry alone; optional ping between VTI addresses succeeded
- [ ] Persistence of the configuration handled per platform guidance (not covered by this source)
