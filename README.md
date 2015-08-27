# HTTPS, HTTP/2 and WordPress

[HTTP/2](https://http2.github.io/) is the next version of the HTTP protocol with
a focus on performance.

[See a demo of HTTP/2](http://www.http2demo.io/)

THe HTTP/2 spec [does not require encryption](https://http2.github.io/faq/#does-http2-require-encryption),
but all browser implementations require encryption. That means implementing HTTP/2
requires HTTPS.

## HTTP/2 support

The latest versions of most major browsers support HTTP/2 (see [compat chart](http://caniuse.com/#feat=http2)).

Both NGINX and Apache are working on HTTP/2 support.

NGINX has a patch for HTTP/2 (see [alpha release notes](https://www.nginx.com/blog/early-alpha-patch-http2/)).
They have not committed to supporting Server Push.

An [Apache module](https://github.com/icing/mod_h2) is in alpha for HTTP/2 support.

[nghttp2](https://nghttp2.org/) includes an HTTP/2 proxy, which is the least invasive
way to support the protocol today.

The HTTP/2 organization lists [a horde of other implementations](https://github.com/http2/http2-spec/wiki/Implementations).

## How to test HTTPS and HTTP/2 locally?

To set up HTTPS a self-signed TLS certificate will be required, here's [a guide](http://www.akadia.com/services/ssh_test_certificate.html).

[VVV](https://github.com/Varying-Vagrant-Vagrants/VVV) creates a certificate and private key
during provisioning, although the default sites are configured for http.
The `siteurl` and `home` options need to be modified to specify the https protocol.

Browsers will issue warnings like "this site might be trying to steal your data"
and a red cross through the HTTPS locl icon. This is because a certificate needs to
be recognized by a certificate authority to validate that you are indeed receiving
data from the organization you think you are, and not a MITM. This can't be fixed,
unless you were to buy a domain name and pass your self-signed certificate to a certificate
authority. Ignore the warnings.

[Fork of VVV with nghttpx as an HTTP/2 proxy](https://github.com/ericandrewlewis/VVV/tree/http2).

## What changes can be made in core to support HTTP/2 better?

The WP function [`is_ssl()`](https://github.com/WordPress/WordPress/blob/master/wp-includes/functions.php#L3748)
doesn't work properly and causes a redirect loop when serving the application through an HTTPS proxy
that runs the web server over HTTP.

Should concatenation of assets be modified for HTTP/2?

## How to upgrade a WP site to HTTPS / HTTP/2?

[Zack has done a ton of this already](https://www.tollmanz.com/wordpress-https-mixed-content-detector-1-1-0-update/). A canonical step-by-step guide would be good.

Placing WordPress behind an HTTPS proxy that requests WordPress over HTTP causes
WordPress to think it's running over HTTP (i.e. `is_ssl()` will return false).
Modifying `$_SERVER` variables in `wp-config.php` is the suggested configuration.
See trac tickets #9235, #19654, #31288.

## Other stuff

[Compare HTTP/1.1 to HTTP/2 with varying latency](http://http2.golang.org/gophertiles?latency=1000)