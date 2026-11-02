---
title: "discoverykit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["DNSMessage", "DNSQuestion", "DNSRecord", "DiscoveryDatagram", "DiscoverySession"]
dependencies: []
symbols: ["DNSMessage", "DNSQuestion", "DNSRecord", "DiscoveryDatagram", "DiscoverySession"]
related: ["api.md", "getting-started.md", "index.md"]
---

# discoverykit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## DNSQuestion

`Public Class DNSQuestion`

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Name` | `String` | No | No | — | — |
| `RecordType` | `Integer` | No | No | — | — |
| `RecordClass` | `Integer` | No | No | — | — |
| `UnicastResponse` | `Boolean` | No | No | — | — |

## DNSRecord

`Public Class DNSRecord`

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Name` | `String` | No | No | — | — |
| `RecordType` | `Integer` | No | No | — | — |
| `RecordClass` | `Integer` | No | No | — | — |
| `CacheFlush` | `Boolean` | No | No | — | — |
| `TTL` | `Integer` | No | No | — | — |
| `Data` | `Bytes` | No | No | — | — |
| `Target` | `String` | No | No | — | — |
| `Priority` | `Integer` | No | No | — | — |
| `Weight` | `Integer` | No | No | — | — |
| `Port` | `Integer` | No | No | — | — |
| `Address` | `String` | No | No | — | — |
| `TextValues` | `Array Of Bytes` | No | No | — | — |

## DNSMessage

`Public Class DNSMessage`

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Identifier` | `Integer` | No | No | — | — |
| `Flags` | `Integer` | No | No | — | — |
| `Questions` | `Array Of DNSQuestion` | No | No | — | — |
| `Answers` | `Array Of DNSRecord` | No | No | — | — |
| `Authorities` | `Array Of DNSRecord` | No | No | — | — |
| `Additionals` | `Array Of DNSRecord` | No | No | — | — |

### IsResponse

`Public Function IsResponse() As Boolean`

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |

### IsTruncated

`Public Function IsTruncated() As Boolean`

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |

### EncodeDNSName

`Public Function EncodeDNSName(name As String) As Bytes`

Encode a DNS presentation name, supporting decimal byte escapes (backslash plus exactly three digits).

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `name` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Bytes` |

### DNSNameLabels

`Public Function DNSNameLabels(name As String) As Array Of Bytes`

Decode a presentation name into owned raw label bytes; UTF-8 conversion is caller-controlled.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `name` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Array Of Bytes` |

### BuildDNSQuery

`Public Function BuildDNSQuery(name As String, recordType As Integer = 12, identifier As Integer = 0, unicastResponse As Boolean = False) As Bytes`

Single IN-class question. QU requests a unicast response to an mDNS query.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `name` | `String` | `ByVal` | Required |
| `recordType` | `Integer` | `ByVal` | `12` |
| `identifier` | `Integer` | `ByVal` | `0` |
| `unicastResponse` | `Boolean` | `ByVal` | `False` |

#### Returns

| Type |
| --- |
| `Bytes` |

### ParseDNSMessage

`Public Function ParseDNSMessage(data As Bytes, maxPacketBytes As Integer = 9000, maxItems As Integer = 512) As DNSMessage`

Decode one complete datagram. Unknown RRs retain exact raw Data; malformed input rejects atomically.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `data` | `Bytes` | `ByVal` | Required |
| `maxPacketBytes` | `Integer` | `ByVal` | `9000` |
| `maxItems` | `Integer` | `ByVal` | `512` |

#### Returns

| Type |
| --- |
| `DNSMessage` |

## DiscoveryDatagram

`Public Class DiscoveryDatagram`

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Message` | `DNSMessage` | No | No | — | — |
| `PeerAddress` | `String` | No | No | — | — |
| `PeerPort` | `Integer` | No | No | — | — |

## DiscoverySession

`Public Class DiscoverySession`

Explicit-lifecycle IPv4 transport. No socket is opened until Open is called.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `LocalPort` | `Integer` | Yes | No | — | — |

### Open

`Public Sub Open(localPort As Integer = 0, joinMulticast As Boolean = False, interfaceAddress As String = "0.0.0.0")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `localPort` | `Integer` | `ByVal` | `0` |
| `joinMulticast` | `Boolean` | `ByVal` | `False` |
| `interfaceAddress` | `String` | `ByVal` | `"0.0.0.0"` |

Returns: no value.

### SendQuery

`Public Sub SendQuery(name As String, recordType As Integer = 12, destination As String = "224.0.0.251", destinationPort As Integer = 5353, identifier As Integer = 0, unicastResponse As Boolean = False)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `name` | `String` | `ByVal` | Required |
| `recordType` | `Integer` | `ByVal` | `12` |
| `destination` | `String` | `ByVal` | `"224.0.0.251"` |
| `destinationPort` | `Integer` | `ByVal` | `5353` |
| `identifier` | `Integer` | `ByVal` | `0` |
| `unicastResponse` | `Boolean` | `ByVal` | `False` |

Returns: no value.

### HasPendingDatagram

`Public Function HasPendingDatagram() As Boolean`

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |

### Receive

`Public Function Receive(maxPacketBytes As Integer = 9000, maxItems As Integer = 512) As Optional Of DiscoveryDatagram`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `maxPacketBytes` | `Integer` | `ByVal` | `9000` |
| `maxItems` | `Integer` | `ByVal` | `512` |

#### Returns

| Type |
| --- |
| `Optional Of DiscoveryDatagram` |

### Close

`Public Sub Close()`

No parameters.

Returns: no value.
