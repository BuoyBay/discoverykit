# DiscoveryKit 0.1.0

Portable bounded DNS/DNS-SD wire parsing and explicit IPv4 mDNS query transport.
Copy `packages/discoverykit-0.1.0.buipkg` into a project's `packages/` directory
and `Import discoverykit`. No external library, OS Bonjour daemon or shell is
required. The [offline example](../examples/discoverykit/main.bui) prints a
PTR query without opening sockets.

## Wire API

| Function | Contract |
| --- | --- |
| `EncodeDNSName(name)` | Encode a presentation name as uncompressed DNS labels |
| `DNSNameLabels(name)` | Owned raw Bytes per label, including escaped/binary labels; root returns an empty array |
| `BuildDNSQuery(name, recordType = 12, identifier = 0, unicastResponse = False)` | One IN-class question, no recursion desired; `12` is PTR, `33` SRV, `16` TXT, `1` A, `28` AAAA |
| `ParseDNSMessage(data, maxPacketBytes = 9000, maxItems = 512)` | Decode one entire DNS datagram or throw `InvalidArgumentException` |

Presentation names may have a final dot; decoded names always do. The root is
`.`. Backslash escapes require exactly three decimal digits and denote one byte:
`\046` embeds a dot inside a label and `\000` embeds a NUL byte. Decoding escapes
bytes outside printable ASCII, spaces, dots and backslashes, preserving arbitrary
DNS label bytes without putting NUL or invalid UTF-8 in Buoy Strings. Unicode input
is encoded as UTF-8 label bytes; this is not IDNA conversion or Unicode case folding.
An escaped and literal ASCII letter may have different presentation strings even
when their wire names are identical. Do not use presentation strings as untrusted
filesystem paths or assume Unicode instance names are printable without decoding.
Use `DNSNameLabels` and explicitly decode each label as UTF-8 when appropriate;
opaque or invalid UTF-8 labels remain byte buffers rather than lossy display text.

`DNSMessage` exposes `Identifier`, raw `Flags`, `Questions`, `Answers`,
`Authorities` and `Additionals`; `IsResponse()` and `IsTruncated()` inspect flags.
Questions expose `Name`, `RecordType`, `RecordClass` and `UnicastResponse` (QU).
Records expose `Name`, `RecordType`, `RecordClass`, `CacheFlush`, unsigned `TTL`
in an Integer, and detached raw `Data` Bytes. Type-specific fields are:

- PTR/CNAME/NS: `Target` presentation name.
- SRV: `Priority`, `Weight`, `Port` and `Target`.
- TXT: ordered `TextValues As Array Of Bytes`; empty strings, binary values,
  duplicate keys and original casing are retained. Decode application text explicitly.
  Zero-length TXT RDATA is interpreted as one empty string, as RFC 6763 requires
  receivers to tolerate, rather than rejecting the rest of the datagram.
- A/AAAA: `Address`; IPv6 uses eight explicit four-digit hexadecimal groups.
- Unknown types: exact `Data`, with no interpretation.

These are mutable result objects, detached from the input packet. They are not
cached; changing one result cannot update or poison future parsing. In raw `Data`
from a compressed record, name pointers refer to the original packet; use decoded
`Target` for names rather than interpreting that buffer as a standalone packet.
Record TTL 0 is retained so applications can recognize goodbye records; no cache
or expiration policy is implicitly applied. A truncated message whose declared
sections are incomplete rejects; a complete packet with TC set returns a result
with `IsTruncated() = True`.

## Opt-in transport

Constructing `New DiscoverySession()` opens no socket. `Open(localPort = 0,
joinMulticast = False, interfaceAddress = "0.0.0.0")` binds IPv4 UDP. The default
uses an ephemeral port, suitable for legacy unicast replies. Explicit
`Open(5353, True)` joins the mDNS group `224.0.0.251`; pass a local IPv4 address
as the third argument to choose the receiving interface. Outgoing multicast TTL
is set to 255. Joining requires local port 5353. Buoy may permit shared port binds;
unicast replies to a shared port may be delivered to another process. Receive-group membership does
not select the outgoing interface: Buoy does not expose that setting.

`SendQuery(name, recordType = 12, destination = "224.0.0.251",
destinationPort = 5353, identifier = 0, unicastResponse = False)` sends immediately.
Set `unicastResponse` to request the QU reply bit. A PTR query for
`_http._tcp.local.` asks for HTTP service instances; query each returned PTR
`Target` for SRV and TXT, then its SRV target for A/AAAA. Responders may include
those records in the additional section already. This library returns records
and peer metadata; the application correlates replies and assembles services.
Destination accepts literal IPv4 only, matching Buoy's public socket API.

`HasPendingDatagram()` checks the incoming queue. `Receive(maxPacketBytes = 9000,
maxItems = 512)` drains one whole datagram and returns `Optional Of DiscoveryDatagram`.
None means no datagram; `.Value.Message`, `.PeerAddress` and `.PeerPort` are
available when `.HasValue` is true. A malformed or over-budget datagram is
consumed and throws; subsequent queued datagrams remain available. `Close()`
is idempotent and terminal; reopening or using a closed session throws. Keep a
session on one application thread, pump Buoy's run loop while waiting, and bound
waits in application code. Native `SocketException` values propagate.

No permission prompt, multicast membership, query or advertisement happens merely
by importing the package. Automated tests send only to IPv4 loopback. Live
multicast operation requires explicit calls and depends on firewall/interface policy.

## Bounds and limits

The parser accepts packet budgets 12..65535 bytes and item budgets 0..4096;
the total number of questions and all three RR sections must fit `maxItems`.
Names enforce 63-byte labels, 255-byte expanded wire length and at most 128
compression-pointer jumps. Labels and the terminator do not consume the jump
budget, preserving compressed names at the full legal length. Compression pointers must point backward into the DNS body.
Reserved label encodings, cycles, truncated fields, malformed known RDATA and
trailing bytes reject. The parser never follows a pointer without checking bounds.
Original RDATA name bytes cannot escape their field even when a pointer refers
elsewhere. Unsupported RR types remain opaque. This deliberately strict decoder
is not a forensic parser for broken DNS captures.

Transport reads up to 65535 bytes before enforcing the parser's budget; that
budget bounds parsing, not the underlying runtime's UDP receive queue. This is
not a complete Bonjour daemon: no advertising/responder, conflict probing,
automatic retries, known-answer suppression, cache/TTL expiration, service merging,
IPv6 multicast transport, DNSSEC verification or incoming IP TTL validation.
Incoming IP TTL is not exposed by Buoy's public API. Packets and discovered
services are untrusted network input; discovery does not authenticate a peer.

## Build and verification

Maintainers run the private validation suite before release. See the repository README for public build instructions.

Linux x86_64 execution is verified. macOS/Windows are compiler-checked; live
multicast, cross-host service discovery and native execution on those platforms
remain unperformed. `BUOY` and `BPM_PACKAGER` select compiler and sealer commands.
Rebuild the checked-in archive with `/usr/bin/buoy pkg seal libraries/discoverykit
-o packages/discoverykit-0.1.0.buipkg`. See [provenance](../PROVENANCE.md).
