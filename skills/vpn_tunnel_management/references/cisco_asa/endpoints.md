# Cisco ASA VTI — Command Wiring (CLI)

Literal ASA CLI syntax for each operation named in `procedure.md`. ASA VTI configuration is
CLI-driven, so this file holds the commands themselves (there is no REST route here); each
heading below matches an operation name used in `procedure.md`. Commands are entered in the
mode shown in the prompt prefix; `<angle-bracket>` items are placeholders for the caller's own
values.

Source: Cisco, "Virtual Tunnel Interface" chapter of the ASA configuration guide (PDF supplied
by the contributor; the chapter's own version label is not printed on its pages, but its feature
history runs through ASA 9.19(1)), read 2026-10-06. Syntax is re-expressed from that chapter;
anything not in it is marked as an assumption or unknown inline.

Prompt convention below: `(config)#` global configuration, `(config-ipsec-proposal)#`,
`(config-ipsec-profile)#`, `(config-if)#` interface configuration (tunnel or virtual template),
`(config-tunnel-ipsec)#` tunnel-group IPsec attributes.

## Create IKEv1 transform set

```
(config)# crypto ipsec ikev1 transform-set <set-name> <encryption> <authentication>
```

- `<encryption>`: `esp-aes` (128-bit), `esp-aes-192`, `esp-aes-256`, `esp-null`.
- `<authentication>`: `esp-md5-hmac`, `esp-sha-hmac`, `esp-none`.

## Create IKEv2 IPsec proposal

```
(config)# crypto ipsec ikev2 ipsec-proposal <proposal-name>
(config-ipsec-proposal)# protocol esp encryption <algorithm> [<algorithm> ...]
(config-ipsec-proposal)# protocol esp integrity <algorithm> [<algorithm> ...]
```

- Encryption keywords: `aes`, `aes-192`, `aes-256`, `aes-gcm`, `aes-gcm-192`, `aes-gcm-256`, `null`.
- Integrity keywords: `sha-1`, `sha-256`, `sha-384`, `sha-512`, `null`.
- The chapter's worked example sets integrity to `null` alongside `aes-gcm-256` encryption (a GCM
  suite). Whether `null` integrity is valid with non-GCM encryption is not stated — unknown.
- Whether the integrity line is mandatory is not stated — unknown.

## Create IPsec profile

```
(config)# crypto ipsec profile <profile-name>
```

## Bind a proposal to the IPsec profile

IKEv1 (transform set):

```
(config-ipsec-profile)# set ikev1 transform-set <set-name>
```

IKEv2 (IPsec proposal):

```
(config-ipsec-profile)# set ikev2 ipsec-proposal <proposal-name>
```

## Set SA lifetime (optional)

```
(config-ipsec-profile)# set security-association lifetime {seconds <number> | kilobytes {<number> | unlimited}}
```

The chapter's example gives both a seconds and a kilobytes value on one line; whether both may be
combined in a single entry is shown only by that example. Default values and valid ranges are not
stated in the chapter — unknown.

## Set responder-only (optional)

```
(config-ipsec-profile)# responder-only
```

Assumption: the chapter lists this under the IPsec profile steps without printing a prompt, so
profile configuration mode is inferred.

## Set PFS group (optional)

```
(config-ipsec-profile)# set pfs group14
```

Only `group14` is shown in the chapter; the full list of accepted groups is not stated — unknown.

## Set trustpoint (optional)

```
(config-ipsec-profile)# set trustpoint <trustpoint-name>
```

## Enable reverse route injection (optional)

```
(config-ipsec-profile)# set reverse-route [dynamic]
```

## Create static VTI interface

```
(config)# interface tunnel <tunnel-id>
(config-if)# nameif <interface-name>
(config-if)# ip address <ip-address> <subnet-mask>
(config-if)# tunnel source interface <source-interface-name>
(config-if)# tunnel destination <peer-ip-address>
(config-if)# tunnel mode ipsec ipv4
(config-if)# tunnel protection ipsec profile <profile-name>
```

Optional, instead of a fixed address (borrowed from another interface):

```
(config-if)# ip unnumbered <interface-name>
(config-if)# ipv6 unnumbered <interface-name>
```

Optional traffic selector (without it, the static VTI proposes any-to-any selectors):

```
(config)# access-list <acl-name> extended permit ip <source> <source-mask> <destination>
(config-if)# tunnel protection ipsec policy <acl-name>
```

Notes:

- Tunnel ID range printed in the chapter is 0 to 10413, but the chapter elsewhere caps static VTIs at
  1024 per device. This is a source inconsistency; see `procedure.md` Known limitations.
- Assumption: `tunnel mode ipsec ipv6` is the IPv6 form for a static VTI. The chapter prints the
  `{ipv4 | ipv6}` choice only for the dynamic template, though it states IPv6 static VTIs are
  supported.
- The chapter's `ip unnumbered` text in the static section is a copy of the dynamic-template wording
  ("virtual template inherits"); it is treated here as the same command applying to a static tunnel
  interface, per the chapter's feature history, which says loopback-inherited addressing applies to
  both static and dynamic VTIs. Whether `ip address` and `ip unnumbered` are mutually exclusive is
  not stated — unknown.

## Create dynamic VTI virtual template

```
(config)# interface virtual-Template <template-number> type tunnel
(config-if)# nameif <interface-name>
(config-if)# ip unnumbered <interface-name>
(config-if)# ipv6 unnumbered <interface-name>
(config-if)# tunnel source interface <source-interface-name>
(config-if)# tunnel mode ipsec {ipv4 | ipv6}
(config-if)# tunnel protection ipsec profile <profile-name>
```

- Template number range printed in the chapter: 1 to 10413.
- `tunnel source interface` is optional; see `procedure.md` for the effect of omitting it.
- Virtual access interfaces are named `<template-nameif>_va<n>` (for a template nameif `dVTI101`:
  `dVTI101_va1`, `dVTI101_va2`, ...).
- Modifying an existing template requires first running `shutdown` on it (interface configuration
  mode). No re-enable command is printed in the chapter; `no shutdown` is the assumed counterpart.

## Attach virtual template to a tunnel group

```
(config)# tunnel-group <tunnel-group-name> type <type>
(config)# tunnel-group <tunnel-group-name> ipsec-attributes
(config-tunnel-ipsec)# virtual-template <template-number>
```

The chapter's example uses type `ipsec-l2l`.

## Enable dynamic-VTI route exchange on a tunnel group

```
(config)# tunnel-group <tunnel-group-name> ipsec-attributes
(config-tunnel-ipsec)# ikev2 route set interface
(config-tunnel-ipsec)# ikev2 route accept any
```

`ikev2 route accept any` is enabled by default per the chapter.

## IKE-side prerequisites (shown only in the chapter's worked example)

The chapter's step-by-step sections do not walk through these; they appear only in its IKEv2 static
VTI example and its "before you begin" text. Values in `<>` are placeholders; the example's own
algorithm choices are illustrative, not documented requirements.

```
(config)# crypto ikev2 policy <policy-number>
(config-ikev2-policy)# encryption <algorithm>
(config-ikev2-policy)# integrity <algorithm>
(config-ikev2-policy)# group <dh-group>
(config-ikev2-policy)# prf <prf-algorithm>
(config-ikev2-policy)# lifetime seconds <number>
(config)# tunnel-group <peer-ip-address> ipsec-attributes
(config-tunnel-ipsec)# ikev2 remote-authentication pre-shared-key <pre-shared-key>
(config-tunnel-ipsec)# ikev2 local-authentication pre-shared-key <pre-shared-key>
(config)# crypto ikev2 enable <interface-name>
```

- `<pre-shared-key>` must be supplied by the caller from their own secret store; the chapter's
  example uses a trivial sample string, which is not reproduced here.
- Certificate-based authentication is mentioned in the chapter (trustpoint under the tunnel group
  for IKEv2 on both ends; for IKEv1 the initiator names the trustpoint, the responder names it in
  the tunnel-group command) but no command syntax is given for the tunnel-group trustpoint — unknown.
- The example tunnel-group line omits a `type`; whether one is required for the static-VTI peer
  entry is not stated — unknown.

## ACL-bypass and hairpin options (global, security-relaxing)

```
(config)# sysopt connection permit-vpn
(config)# same-security-traffic permit intra-interface
```

- `sysopt connection permit-vpn` lets IPsec-originated traffic through without checking interface
  ACLs.
- The `same-security-traffic ... intra-interface` form is needed when an ACL on the VTI must be hit
  while the outside and VTI interfaces are both at security level 0 (the VTI's level is fixed at 0).
  The chapter names the command and its `intra-interface` argument only; the `permit` keyword is
  assumed from the standard ASA form.

## Peer-side note (Cisco IOS peer)

If the peer sends IKEv2 configuration-request payloads, the ASA cannot form the IKEv2 tunnel. On an
IOS peer the chapter shows disabling this inside the IKEv2 profile:

```
(config-ikev2-profile)# no config-exchange request
```

## Error strings

The chapter documents no error message text. No literal error strings to pattern-match — unknown.
