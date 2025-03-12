# Development Update 24.30

## Overview

This is an update (code drop) to incorporate changes made to support
the Intel&reg; IPU E2100.

This update aligns with [P4CP Release v3.1.0.0](https://github.com/ipdk-io/networking-recipe/releases/tag/v3.1.0.0)

This includes `mev-ts-1.5`, `mev-ts-1.6`, and partially `mev-ts-1.7`.

## Breaking Changes

### Linux Networking v3

- The P4 Control Plane Linux Networking has been upgraded from `v2` to `v3`. Linux Networking v3 upgrades the P4 program to combine and integrate the OVS offload and [IPDK IPsec offload recipe](https://github.com/ipdk-io/ipsec-recipe)
- The Linux Networking v2 is no longer supported

## What's Changed

- The Linux Networking program has been upgraded from v2 to v3, now called `Linux Networking v3`
- Various unit tests have been added in the ovs-p4rt space
- Geneve Tunnel Support in Linux Networking
- Support for LAG LACP (802.3ad) mode in Linux Networking
- Support for setting and resetting the default action/entry of LEM/SEM/WCM/LPM tables
- P4 Role Configuration which allows simultaneous access for multiple controllers. This allows the P4 Control Plane to work in conjunction with IPsec control plane via the strongSwan plugin

## Component Changes

### OVS

Changes to support Linux Networking v3, including introduction of an option to specify gRPC address when launching `ovs-vswtichd`

Changes related to Geneve tunnel support

### Stratum-deps

Housekeeping work in stratum-deps to fix issues and better documentation history capture.

Latest release of stratum-deps is v1.3.3

### krnlmon

Major changes introduced in krnlmon to move from support of Linux Networking v2 to Linux Networking v3. All code supporting Linux Networking v2 has been removed.

## Security Fixes

No changes. The latest zlib still has the [CVE-2023-45853](https://nvd.nist.gov/vuln/detail/CVE-2023-45853) active and a fix is not available.

    Note that the CVE is is part of an experimental MiniZip utility that is unused/unsupported by zlib.
