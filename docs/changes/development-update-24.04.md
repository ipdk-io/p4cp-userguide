# Development Update 24.04

## Overview

This is an update (code drop) to incorporate changes made to support
the Intel&reg; IPU E2100.

This update aligns with [IPDK v24.01 release](https://github.com/ipdk-io/networking-recipe/releases/tag/v24.01)

## Breaking Changes

### Client Flags

- The Stratum `CredentialsManager` class and `gnmi_cli` tool have been aligned
  with the current upstream versions, and IPDK modifications have been
  revised to be more consistent with Stratum as a whole.

  The `-grpc_client_cert_req_type` flag is now `-client_cert_req_type`, and
  its values have been shortened. They are also now case-insensitive.

  | Old Name | New Name |
  | -------- | -------- |
  | NO_REQUEST_CLIENT_CERT | no_request |
  | REQUEST_CLIENT_CERT_NO_VERIFY | request_no_verify |
  | REQUEST_CLIENT_CERT_AND_VERIFY | request_and_verify |
  | REQUIRE_CLIENT_CERT_NO_VERIFY | require_no_verify |
  | REQUIRE_CLIENT_CERT_AND_VERIFY | require_and_verify |

  This change affects all clients that use CredentialsManager, including
  `gnmi_cli`, `sgnmi_cli`, and `gnmi-ctl`.

## What's Changed

- The Linux Networking program has been upgraded from v1 to v2, now called `Linux Networking v2`
- Exception packet handling - Enhanced Linux Networking with multiple VNI support, port to bridge association and VLAN traffic offload (Intel IPU&reg; E2100 target only).
- Link Aggregation Group (LAG) - Supports active-backup use case of LAG on IDPF interfaces done via bonding driver. This LAG interface is used only for underlay connectivity in Exception packet handling feature. (Intel IPU&reg; E2100 target only).
- Packet IO - Facilitates the exchange of packets between control plane applications and P4 dataplane (Intel IPU&reg; E2100 target only).
- Indexed and direct meters in policer mode - Metering along with policer allows the users to determine the amount of data used and then control the usage. (Intel IPU&reg; E2100 target only).

## Component Changes

### Stratum-deps

Introduction of the [IPDK stratum-deps](https://github.com/ipdk-io/stratum-deps) repository.

In order to avoid frequent re-building of dependent libraries such as gRPC, protobuf etc, which do not change on a regular basis, a stratum-deps repository has been created. You can build these dependencies once and pass the install path to build `infrap4d`, making for a much quicker build process.

Latest release of stratum-deps is v1.3.0

### Stratum

Stratum has been updated to the latest version. All changes made between
3-Jun-2021 and 7-Dec-2023 have been downstreamed and integrated into
P4 Control Plane.

The majority of the changes were either global housekeeping (such as
formatting files with clang-format and buildifier) or do not affect
P4 Control Plane (such as updates to the `barefoot` platform, of which
there were many).

Changes include:

- Defaulting to `--test_output=errors` when running `bazel test`, so the
  the test log becomes part of the console output if there are errors.

- Adding support for the OpenConfig `/interfaces/interface[name=*]/state/id`
  path.

- Replacing the raw text proto string terminal `PROTO` with `pb`. This
  fixes compiler errors in newer versions of gcc and clang.

- Replacing P4Runtime controller management with controller code from the
  PINS project.

- Adding support for P4Runtime role configuration.

- Adding new unit tests and updating existing tests.

- Overhauling `lib/security` (credentials management).

- Improvements to log output.

For more information, see the commit comments in the upstream repository.

We have also made minimal updates to the `barefoot` platform so we can
build it with version 9.11.0 of the Barefoot SDE (the version used to
build the TDI `tofino` target).

Note that we have _not_ attempted to migrate changes made in the Barefoot
platform to the TDI platform (which is based on Barefoot).
This can be done later, if the need arises.

## Security Fixes

- Upgrade from OpenSSL 1.1.1x to 3.x
- Library upgrades
  - Upgraded gRPC to 1.59.2 to address [CVE-2023-33953](https://nvd.nist.gov/vuln/detail/cve-2023-33953), [CVE-2023-4785](https://nvd.nist.gov/vuln/detail/cve-2023-4785)
  - Upgraded protobuf to version 25.0
  - Upgraded abseil-cpp to version 20230802.0
  - Upgraded zlib to version 1.3 to address [CVE-2023-45853](https://nvd.nist.gov/vuln/detail/CVE-2023-45853).
    - Note that the CVE is still present in zlib 1.3 and part of an experimental MiniZip utility that is unused/unsupported by zlib.

## P4Runtime Fork

P4 Control Plane uses a fork of the `p4runtime` repository to add provisional
support for the PacketModMeter and DirectPacketModMeter externs.

We plan to revert to using the standard `p4runtime` repository when the
additions become standard.

Build artifacts for Python, Go, and C++ can be downloaded from the
[Releases page](https://github.com/ipdk-io/p4runtime-dev/releases)
of the repository. The current version is 2023.11.0.

If you wish to build the protobuf artifacts yourself, see the
[README file](https://github.com/ipdk-io/networking-recipe/blob/main/protobufs/README.md)
for instructions.