# DNS

`.iw` is not a real, IANA-delegated TLD, so it isn't reachable by recursing
against the real DNS root. It's served as an overlay on top of it: our
resolvers use the real root (and its root hints) for everything else, and
answer `.iw` themselves.

## Using our resolvers

The simplest option: set your DNS server to one of

```
10.21.0.1   (de-fra01, master for .iw)
10.21.0.3   (it-mil01)
10.21.0.4   (pl-waw01)
```

These recurse for everything, including `.iw`, and validate DNSSEC.

## Running your own recursive resolver

If you run your own recursive resolver on the Intraweb (or reachable over
it) and want it to also resolve and validate `.iw` itself, forward just that
zone to one of the resolvers above and pin the `.iw` trust anchor, since
there's no parent zone to deliver it via a normal chain of trust. For BIND:

```
zone "iw" {
  type forward;
  forward only;
  forwarders { 10.21.0.1; };
};

trust-anchors {
  iw. static-key <flags> <protocol> <algorithm> "<base64 key>";
};
```

The current values go in the `static-key` line above. Rather than duplicate
them here (and have them go stale), they're published as the `ds-rdata` on
the [`iw` object in the registry](https://github.com/itsvic-dev/intraweb-registry/blob/trunk/data/dns/iw) —
that's the same DS-record format every delegated `.iw` zone publishes, just
with no parent to register it with, so treat it as the trust anchor to
configure by hand instead.

## DNSSEC for your own zone

If you operate a zone under `.iw`, you can sign it and publish its DS record
the normal way: generate a key, compute the DS record, and add it as
`ds-rdata` on your zone's object in the registry. See the other `.iw` zones'
DNS objects there for the expected format.
