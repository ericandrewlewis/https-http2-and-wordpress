## Transition a site to HTTP/2

### Set up a testing environment for enabling HTTP/2

We've created [a fork of VVV with HTTP/2 enabled](https://github.com/ericandrewlewis/VVV/tree/http2).
This is done by using nghttpx as an HTTP/2 proxy.

## What changes can be made in core to support HTTP/2 better?

The WP function [`is_ssl()`](https://github.com/WordPress/WordPress/blob/master/wp-includes/functions.php#L3748)
doesn't work properly and causes a redirect loop when serving the application through an HTTPS proxy
that runs the web server over HTTP.

Should concatenation of assets be modified for HTTP/2?