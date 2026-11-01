# Provenance

Independent Buoy implementation; no Bonjour, Avahi, Xojo or third-party DNS
library source is copied. No native bridge or external data table is bundled.
The deterministic Python fixture writer uses only Python's standard library.

Protocol references:

- [RFC 1035](https://www.rfc-editor.org/rfc/rfc1035), DNS labels, compression,
  messages, resource records and TXT character-strings.
- [RFC 2782](https://www.rfc-editor.org/rfc/rfc2782), SRV priority/weight/port/target.
- [RFC 3596](https://www.rfc-editor.org/rfc/rfc3596), IPv6 AAAA record bytes.
- [RFC 6762](https://www.rfc-editor.org/rfc/rfc6762), mDNS IPv4 group/port,
  QU/cache-flush class bits and outgoing TTL 255.
- [RFC 6763](https://www.rfc-editor.org/rfc/rfc6763), DNS-SD PTR/SRV/TXT discovery
  flow and binary TXT values.

These references define wire formats; the package does not claim to implement
every protocol requirement. README.md states its subset and transport limits.
