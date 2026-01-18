# DNS

We currently have a single DNS root server, which is also a recursive DNS
server. This server lives at `10.21.0.1` (`v.root-servers.iw`).

If you wish to run your own recursive DNS server, you will need to create a
custom root zone hints file with contents like this:

```
.                   3600  IN  NS  v.root-servers.iw.
v.root-servers.iw.  3500  IN  A   10.21.0.1
```
