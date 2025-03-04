[![Continuous integration](https://github.com/fujiapple852/trippy/workflows/CI/badge.svg)](https://github.com/fujiapple852/trippy/actions/workflows/ci.yml)
[![Crate](https://img.shields.io/crates/v/trippy.svg)](https://crates.io/crates/trippy/0.6.0)
[![Packaging status](https://repology.org/badge/tiny-repos/trippy.svg)](https://repology.org/project/trippy/versions)
[![Status](https://img.shields.io/badge/status-alpha-blue)](https://github.com/fujiapple852/trippy)
[![Gitter](https://badges.gitter.im/trippy-rs/community.svg)](https://gitter.im/trippy-rs/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

# Trippy

A network diagnostic tool, inspired by [mtr](https://github.com/traviscross/mtr).

<img src="assets/trippy-0.3.0-08-05-2022.gif" alt="trippy"/>

Trippy combines the functionality of traceroute and ping and is designed to assist with the diagnosis of network issues.

## Features

- Trace using multiple protocols:
    - `ICMP`, `UDP` & `TCP`
    - `IPv4` & `IPv6`
- Customizable tracing options:
    - packet size & payload pattern
    - start and maximum time-to-live (TTL)
    - minimum and maximum round duration
    - round end grace period & maximum number of unknown hops
    - source & destination port (`TCP` & `UDP`)
    - Equal Cost Multi-path Routing strategies (`classic`, `paris` and `dublin`)
    - source address and source interface
    - `TOS` (aka `DSCP + ECN`)
- Tui interface:
    - Trace multiple targets simultaneously from a single instance of Trippy
    - Per hop stats (sent, received, loss%, last, avg, best, worst, stddev & status)
    - Per hop round-trip-time (RTT) history and frequency distributing charts
    - Zoom-able chart of RTT for all hops in a trace
    - Multiple hosts per hop with ability to cap display to N hosts and show frequency %
    - Freeze/unfreeze the Tui, reset the stats, flush the cache, preserve screen on exit
    - Responsive UI with adjustable refresh rate
- DNS:
    - use system, external (Google `8.8.8.8` or Cloudflare `1.1.1.1`) or custom resolver
    - Lazy reverse DNS queries
    - Lookup [autonomous system](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) number (ASN) and name
- Generate tracing reports:
    - `json`, `csv` & tabular (pretty-printed and markdown)
    - configurable reporting cycles
- Runs on multiple platform (macOS, Linux, NetBSD)
- Capabilities aware application (Linux only)

### Equal Cost Multi-path Routing

Support for [Equal Cost Multi-path Routing](https://en.wikipedia.org/wiki/Equal-cost_multi-path_routing)
strategies are shown in the following table (see
the [tracking issue](https://github.com/fujiapple852/trippy/issues/274)):

| Strategy | Family      | Protocol  | Status |
|----------|-------------|-----------|--------|
| Classic  | IPv4 / IPv6 | UDP / TCP | ✅      |
| Paris    | IPv4 / IPv6 | UDP / TCP | ❌      |
| Dublin   | IPv4        | UDP       | ✅      |
| Dublin   | IPv4        | TCP       | ❌      |
| Dublin   | IPv6        | UDP / TCP | ❌      |

## Distributions

### Cargo

[![Crates.io](https://img.shields.io/crates/v/trippy)](https://crates.io/crates/trippy/0.6.0)

```shell
cargo install trippy
```

### Homebrew

[![homebrew version](https://img.shields.io/badge/homebrew-0.6.0-blue)](https://github.com/fujiapple852/homebrew-trippy)

```shell
brew tap fujiapple852/trippy && brew install trippy
```

### NetBSD

[![pkgsrc current package](https://repology.org/badge/version-for-repo/pkgsrc_current/trippy.svg)](https://repology.org/project/trippy/versions)

```shell
pkgin install trippy
```

### Pacman (Arch Linux)

[![Arch package](https://repology.org/badge/version-for-repo/arch/trippy.svg)](https://repology.org/project/trippy/versions)

```shell
pacman -S trippy
```

### Snap

[![trippy](https://snapcraft.io/trippy/badge.svg)](https://snapcraft.io/trippy)

```shell
snap install trippy
```

### Docker

[![Docker Image Version (latest by date)](https://img.shields.io/docker/v/fujiapple/trippy)](https://hub.docker.com/r/fujiapple/trippy/)

```shell
docker run -it fujiapple/trippy
```

## Platforms

Trippy has been (lightly...) tested on the following platforms:

### IPv4

| Platform | ICMP | UDP | TCP | Notes                                                         |
|----------|------|-----|-----|---------------------------------------------------------------|
| Linux    | ✅    | ✅   | ✅   |                                                               |
| macOS    | ✅    | ✅   | ✅   |                                                               |
| NetBSD   | ✅    | ✅   | ✅   |                                                               |
| OpenBSD  | ⏳    | ⏳   | ⏳   | See [#213](https://github.com/fujiapple852/trippy/issues/213) |
| FreeBSD  | ⏳    | ⏳   | ⏳   | See [#214](https://github.com/fujiapple852/trippy/issues/214) |
| Windows  | ❌    | ❌   | ❌   | See [#98](https://github.com/fujiapple852/trippy/issues/98)   |

### IPv6

| Platform | ICMP | UDP | TCP | Notes                                                         |
|----------|------|-----|-----|---------------------------------------------------------------|
| Linux    | ✅    | ✅   | ✅   |                                                               |
| macOS    | ✅    | ✅   | ✅   |                                                               |
| NetBSD   | ✅    | ✅   | ✅   |                                                               |
| OpenBSD  | ⏳    | ⏳   | ⏳   | See [#213](https://github.com/fujiapple852/trippy/issues/213) |
| FreeBSD  | ⏳    | ⏳   | ⏳   | See [#214](https://github.com/fujiapple852/trippy/issues/214) |
| Windows  | ❌    | ❌   | ❌   | See [#98](https://github.com/fujiapple852/trippy/issues/98)   |

## Privileges

Trippy uses a raw socket which require elevated privileges. Enabling the required privilege can be achieved in several
ways, including:

1: Run as `root` user via `sudo`:

```shell
sudo trip www.bitwizard.nl
```

2: `chown` `trip` as the `root` user and set the `setuid` bit:

```shell
sudo chown root $(which trip) && sudo chmod +s $(which trip)
```

3: [Linux only] Set the `CAP_NET_RAW` capability:

```shell
sudo setcap CAP_NET_RAW+p $(which trip)
```

Trippy is a capability aware application and will add `CAP_NET_RAW` to the effective set if it is present in the allowed
set. Note that trippy will drop all capabilities after creating the raw socket.

## Usage Examples

Basic usage with default parameters:

```shell
trip www.bitwizard.nl
```

Trace using the `udp` (or `tcp` or `icmp`) protocol (also aliases `--udp` & `--tcp`):

```shell
trip www.bitwizard.nl -p udp
```

Trace to multiple targets simultaneously (`icmp` protocol only,
see [#72](https://github.com/fujiapple852/trippy/issues/72)):

```shell
trip www.bitwizard.nl google.com crates.io
```

Trace with a minimum round time of `250ms` and a grace period of `50ms`:

```shell
trip www.bitwizard.nl -i 250ms -g 50ms
```

Trace with a custom first and maximum `time-to-live`:

```shell
trip www.bitwizard.nl --first-ttl 2 --max-ttl 10
```

Use custom destination port `443` for `tcp` tracing:

```shell
trip www.bitwizard.nl -p tcp -P 443
```

Use custom source port `5000` for `udp` tracing:

```shell
trip www.bitwizard.nl -p udp -S 5000
```

Use the `dublin` Equal Cost Multi-path Routing strategy for `udp` with fixed source and destination ports:

```shell
trip www.bitwizard.nl -p udp -R dublin -S 5000 -P 3500
```

Trace with a custom source address:

```shell
trip www.bitwizard.nl -p tcp -A 127.0.0.1
```

Trace with a source address determined by the IPv4 address for interface `en0`:

```shell
trip www.bitwizard.nl -p tcp -I en0
```

Trace using `IPv6`:

```shell
trip www.bitwizard.nl -6
```

Generate a `json` (or `csv`, `pretty`, `markdown`) tracing report with 5 rounds of data:

```shell
trip www.bitwizard.nl -m json -c 5
```

Perform DNS queries using the `google` DNS resolver (or `cloudflare`, `system`, `resolv`):

```shell
trip www.bitwizard.nl -r google
```

Lookup AS information for all discovered IP addresses (not yet available for the `system` resolver,
see [#66](https://github.com/fujiapple852/trippy/issues/66)):

```shell
trip www.bitwizard.nl -r google -z
```

## Reference

```shell
USAGE:
    trip [OPTIONS] <TARGETS>...

ARGS:
    <TARGETS>...    A space delimited list of hostnames and IPs to trace

OPTIONS:
    -m, --mode <MODE>
            Output mode [default: tui] [possible values: tui, stream, pretty,
            markdown, csv, json]

    -p, --protocol <PROTOCOL>
            Tracing protocol [default: icmp] [possible values: icmp, udp, tcp]

        --udp
            Trace using the UDP protocol

        --tcp
            Trace using the TCP protocol

    -4, --ipv4
            use IPv4 only

    -6, --ipv6
            Use IPv6 only

    -P, --target-port <TARGET_PORT>
            The target port (TCP & UDP only) [default: 80]

    -S, --source-port <SOURCE_PORT>
            The source port (TCP & UDP only) [default: auto]

    -A, --source-address <SOURCE_ADDRESS>
            The source IP address [default: auto]

    -I, --interface <INTERFACE>
            The network interface [default: auto]

    -i, --min-round-duration <MIN_ROUND_DURATION>
            The minimum duration of every round [default: 1s]

    -T, --max-round-duration <MAX_ROUND_DURATION>
            The maximum duration of every round [default: 1s]

        --initial-sequence <INITIAL_SEQUENCE>
            The initial sequence number [default: 33000]

    -R, --multipath-strategy <MULTIPATH_STRATEGY>
            The Equal-cost Multi-Path routing strategy (IPv4/UDP only) [default:
            classic] [possible values: classic, paris, dublin]

    -g, --grace-duration <GRACE_DURATION>
            The period of time to wait for additional ICMP responses after the
            target has responded [default: 100ms]

    -U, --max-inflight <MAX_INFLIGHT>
            The maximum number of in-flight ICMP echo requests [default: 24]

    -f, --first-ttl <FIRST_TTL>
            The TTL to start from [default: 1]

    -t, --max-ttl <MAX_TTL>
            The maximum number of TTL hops [default: 64]

        --packet-size <PACKET_SIZE>
            The size of IP packet to send (IP header + ICMP header + payload)
            [default: 84]

        --payload-pattern <PAYLOAD_PATTERN>
            The repeating pattern in the payload of the ICMP packet [default: 0]

    -Q, --tos <TOS>
            The TOS (i.e. DSCP+ECN) IP header value (TCP and UDP only) [default:
            0]

        --read-timeout <READ_TIMEOUT>
            The socket read timeout [default: 10ms]

    -r, --dns-resolve-method <DNS_RESOLVE_METHOD>
            How to perform DNS queries [default: system] [possible values:
            system, resolv, google, cloudflare]

        --dns-timeout <DNS_TIMEOUT>
            The maximum time to wait to perform DNS queries [default: 5s]

    -z, --dns-lookup-as-info
            Lookup autonomous system (AS) information during DNS queries

    -a, --tui-address-mode <TUI_ADDRESS_MODE>
            How to render addresses [default: host] [possible values: ip, host,
            both]

    -M, --tui-max-addrs <TUI_MAX_ADDRS>
            The maximum number of addresses to show per hop

    -s, --tui-max-samples <TUI_MAX_SAMPLES>
            The maximum number of samples to record per hop [default: 256]

        --tui-preserve-screen
            Preserve the screen on exit

        --tui-refresh-rate <TUI_REFRESH_RATE>
            The TUI refresh rate [default: 100ms]

    -c, --report-cycles <REPORT_CYCLES>
            The number of report cycles to run [default: 10]

    -h, --help
            Print help information

    -V, --version
            Print version information
```

## Acknowledgements

Trippy is made possible by [tui-rs](https://github.com/fdehau/tui-rs)
, [crossterm](https://github.com/crossterm-rs/crossterm) as well
as [several](https://github.com/fujiapple852/trippy/blob/master/Cargo.toml) foundational Rust libraries.

Trippy draws heavily from [mtr](https://github.com/traviscross/mtr) and also incorporates ideas
from both [libparistraceroute](https://github.com/libparistraceroute/libparistraceroute)
& [Dublin Traceroute](https://github.com/insomniacslk/dublin-traceroute).

The Trippy networking code is inspired by [pnet](https://github.com/libpnet/libpnet) and some elements of that codebase
are incorporated in Trippy.

The [AS](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) data is retrieved from
the [IP to ASN Mapping Service](https://team-cymru.com/community-services/ip-asn-mapping/#dns) provided
by [Team Cymru](https://team-cymru.com).

The [trippy.cli.rs](https://trippy.cli.rs) CNAME hosting is provided by [cli.rs](https://cli.rs).

## License

This project is distributed under the terms of the Apache License (Version 2.0).

Unless you explicitly state otherwise, any contribution intentionally submitted for inclusion in time by you, as defined
in the Apache-2.0 license, shall be licensed as above, without any additional terms or conditions.

See [LICENSE](LICENSE) for details.

Copyright 2022 [Trippy Contributors](https://github.com/fujiapple852/trippy/graphs/contributors)
