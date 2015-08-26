# HTTP/2 and WordPress

## HTTP/2 support

**Browsers** HTTP/2 is supported in the latest version of most major browsers (see [compat chart](http://caniuse.com/#feat=http2)).

**NGINX** HTTP/2 support is in alpha (see [alpha release notes](https://www.nginx.com/blog/early-alpha-patch-http2/)).
They have not committed to Server Push.

**Apache** HTTP/2 support is in alpha via [a module](https://github.com/icing/mod_h2).

[nghttp2](https://nghttp2.org/) includes an HTTP/2 web server and a proxy.

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

## How to upgrade a WP site to HTTPS / HTTP/2?

[Zack has done a ton of this already](https://www.tollmanz.com/wordpress-https-mixed-content-detector-1-1-0-update/). A canonical step-by-step guide would be good.

Placing WordPress behind an HTTPS proxy that requests WordPress over HTTP causes
WordPress to think it's running over HTTP (i.e. `is_ssl()` will return false).
Modifying `$_SERVER` variables in `wp-config.php` is the suggested configuration.
See trac tickets #9235, #19654, #31288.