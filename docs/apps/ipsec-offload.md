# IPsec Offload

The Inline IPsec offload feature uses the
[IPDK Infrastructure Application Interface](https://ipdk.io/documentation/Interfaces/InfraApp/)
to enable cryptographically-secure data traffic. The IPsec control plane
(IKE protocol) is offloaded, thus avoiding specialized drivers or control
planes burdening compute instances. Further details are available on the
[IPDK Inline Acceleration - IPsec](https://ipdk.io/documentation/Recipes/InlineIPsec/)
page.

This feature works in conjunction with the
[IPsec Recipe](https://github.com/ipdk-io/ipsec-recipe), which provides
strongSwan plugin code to configure and program control messages into the
target.

The IPsec offload feature is only supported on the Intel&reg; IPU E2100 target.

## Feature overview

The `infrap4d` process provides the gRPC server-side support for P4RT and
gNMI messages. strongSwan acts as the gRPC client in this context.
You can also use other clients that implement the IKE stack. The
client uses P4Runtime to program the Security Policy Database (SPD), and it
uses gNMI to configure Security Association Database (SAD) entries, which
includes encryption keys, re-keying etc.

The [openconfig-ipsec-offload](https://github.com/ipdk-io/openconfig-public/blob/master/release/models/ipsec/openconfig-ipsec-offload.yang)
YANG model is used to configure IPsec offload.

## Enabling IPsec

Follow the sequence of steps listed below to enable IPsec functionality.
The `fxp-net_linux-networking` P4 program is a combined recipe which implements and supports both the Linux Networking and the IPsec portion of the program.

### Compile P4 program

Compile the ipsec-offload program according to the instructions in
[Compiling P4 programs](/guides/es2k/compiling-p4-programs.md)
to generate crypto P4 artifacts for programming the pipelines.

### Load `fxp-net_linux-networking` P4 package

Follow the instructions in [Deploying P4 programs](/guides/es2k/deploying-p4-programs.md)
to load the hardware FXP pipeline with the IPsec package.

### Configure and run infrap4d

Follow the instructions in
[Running infrap4d](/guides/es2k/running-infrap4d.md)
and prepare the system with generated TDI.json and context.json file references.
In order to  offload IPsec, fixed function support must be enabled in infrap4d.

The /usr/share/stratum/es2k/es2k_skip_p4.conf file must include the fixed
function configuration reference.

```json
"fixed_functions" : [
  {
   "name": "crypto",
   "tdi": "/tmp/fixed/tdi.json",
   "ctx": "/tmp/fixed/crypto-mgr-ctx.json"
  }
],
```

Update this configuration before starting `infrap4d`.

### Configure strongSwan

Follow the instructions in the [strongSwan documentation](https://docs.strongswan.org/docs/5.9/index.html)
to configure host for the use-case selected. This includes details such as
certificate location, IPs, lifetime thresholds, etc.

### Start IPsec

With the strongSwan application configured, starting IPsec
(see [ipsec-recipe](https://github.com/ipdk-io/ipsec-recipe) for details) will
initiate the pipeline, program the SPD rules as per the P4 program, and
configure/re-configure SAD entries based on negotiated encryption parameters
between local and peer system.
