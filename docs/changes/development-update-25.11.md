# Development Update 25.11

## Overview

This is an update (code drop) to incorporate changes made to support
the Intel&reg; IPU E2100.

This update aligns with [P4CP Release v3.4.0](https://github.com/ipdk-io/networking-recipe/releases/tag/v3.4.0)

This includes `mev-ts-1.7` (partially), `mev-ts-1.8`, `mev-ts-1.9`, and `mev-ts-2.0`.

## What's Changed

- The `v3` references for Linux Networking have been dropped. `Linux Networking v3` is hereby simply called `Linux Networking`.
- Housekeeping work for build updates, bug fixes etc
- Addition of unit tests

## Component Changes

### Stratum-deps

The library `re2` has been made a discrete component in order to allow us to upgrade it at will, instead of using the pre-defined submodule version used in gRPC.

Latest release of stratum-deps is v1.3.5

### Stratum

Partial support added to the [/virtual-ports OpenConfig model](https://github.com/ipdk-io/openconfig-public/blob/master/release/models/virtual-devices/openconfig-virtual-ports.yang). Only the VSI, oper-status and mac-address GET operations are supported as of now.

## Security Fixes

- `c-ares` has been upgraded to v1.34.4 to address [CVE-2024-25629](https://nvd.nist.gov/vuln/detail/CVE-2024-25629)
- The latest zlib still has the [CVE-2023-45853](https://nvd.nist.gov/vuln/detail/CVE-2023-45853) active and a fix is not available (Note that the CVE is is part of an experimental MiniZip utility that is unused/unsupported by zlib).
