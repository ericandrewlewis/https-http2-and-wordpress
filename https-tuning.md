# Tuning HTTPS

This is sysadmin-level tuning that web developers should not need to futz with.

## Testing TLS

Limit your browser to 300ms 3g so you can see the effect of TLS handshakes.

## Session Resumption

Openssl comes with a command line SSL client to test a server, `openssl s_client`. You can use this to test session resumption via session identifiers and session tickets.

ssllabs can also test for this.

This command reconnects to a server five times using the same session identifier to test session caching. Using `-nossl2` [somehow avoids old handshake formats](https://www.feistyduck.com/library/openssl-cookbook/online/ch-testing-with-openssl.html#using-different-handshake-formats).

```bash
echo | openssl s_client -connect twitter.com:443 -reconnect -no_ssl2 -no_ticket
````

or maybe this to limit output.

```bash
echo | openssl s_client -connect twitter.com:443 -reconnect -no_ssl2 -no_ticket 2> /dev/null | grep 'New\|Reuse'
```

Servers should share session cache. Read [Cloudflare's article about shared session cache](blog.cloudflare.com/tls-session-resumption-full-speed-and-secure/).

[Tuning session cache](https://timtaubert.de/blog/2014/11/the-sad-state-of-server-side-tls-session-resumption-implementations/)

Session ID data is stored on the server. How do you configure how long to store in the cache, and how to eject? Apache has mod_status provides debug output of cache hit rate for tls cache.

[Session resumption is good for perf but risky as the cache is never purged](https://wiki.mozilla.org/Security/Server_Side_TLS#Session_Resumption).

TLS handshake should take 1 round trip. If it's more than that, fix it.

False start requires various requirements from different browsers. basically Npl/alpn and forward secrecy ciphers (ecdhe).

## Benchmark crypto on server?

eg. openssl speed sha ecdh`

Enable session resumption and false start.

Optimize TLS record size to avoid overflowing a TCP congestion window ([1](https://youtu.be/fQX2mJ11vCs?t=1834))

OCSP stapling

Terminate TLS as close to the user as possible for dynamic requests. I.e. use CDNs for dynamic requests?