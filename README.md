# DNS packets and mDNS

Portable bounded DNS/DNS-SD wire parsing and explicit IPv4 mDNS query transport.
Copy `packages/discoverykit-0.1.0.buipkg` into a project's `packages/` directory
and `Import discoverykit`. No external library, OS Bonjour daemon or shell is
required. The [offline example](examples/discoverykit/main.bui) prints a
PTR query without opening sockets.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add discoverykit --git https://github.com/BuoyBay/discoverykit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## API at a glance

| Function | Contract |
| --- | --- |
| `EncodeDNSName(name)` | Encode a presentation name as uncompressed DNS labels |
| `DNSNameLabels(name)` | Owned raw Bytes per label, including escaped/binary labels; root returns an empty array |
| `BuildDNSQuery(name, recordType = 12, identifier = 0, unicastResponse = False)` | One IN-class question, no recursion desired; `12` is PTR, `33` SRV, `16` TXT, `1` A, `28` AAAA |
| `ParseDNSMessage(data, maxPacketBytes = 9000, maxItems = 512)` | Decode one entire DNS datagram or throw `InvalidArgumentException` |

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer discoverykit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```


## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
