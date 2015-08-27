# HTTPS, HTTP/2 and WordPress

[HTTP/2](https://http2.github.io/) is the next version of the HTTP protocol with
a focus on performance.

[Compare HTTP/1.1 and 2 with varying latency]([Compare HTTP/1.1 to HTTP/2 with varying latency](http://http2.golang.org/gophertiles?latency=1000)).

All browser implementations require encryption, which means HTTPS is a requirement for HTTP/2.

## HTTP/2 support

The latest versions of most major browsers [support HTTP/2](http://caniuse.com/#feat=http2).

Both NGINX and Apache have HTTP/2 support in an alpha stage.

NGINX has a patch for HTTP/2 (see [alpha release notes](https://www.nginx.com/blog/early-alpha-patch-http2/)).
They have not committed to supporting Server Push.

An [Apache module](https://github.com/icing/mod_h2) is in alpha for HTTP/2 support.

[nghttp2](https://nghttp2.org/) includes an HTTP/2 proxy ([nghttpx](https://nghttp2.org/documentation/nghttpx-howto.html)).
This is a way to support the protocol without running alpha versions of web server software.

The HTTP/2 organization lists [a horde of other implementations](https://github.com/http2/http2-spec/wiki/Implementations).

## Transition a site to HTTPS

### Switch any embedded content loaded over HTTP to HTTPS

Zack Tollman's [HTTPS Mixed Content Detector](https://www.tollmanz.com/wordpress-https-mixed-content-detector/)
will log all embedded content loaded over HTTP.

### Set up a testing environment for enabling HTTPS

To configure a site to run HTTPS in a development environment, a self-signed TLS
certificate is required. Here's [a guide](http://www.akadia.com/services/ssh_test_certificate.html).

[VVV](https://github.com/Varying-Vagrant-Vagrants/VVV), a popular development
environment for WordPress, [creates a cert and encryption key](https://github.com/Varying-Vagrant-Vagrants/VVV/blob/v1.1/provision/provision.sh#L233-L246),
although the default sites are [setup for HTTP](https://github.com/Varying-Vagrant-Vagrants/VVV/blob/v1.1/provision/provision.sh#L470).

Browsers will issue warnings like "this site might be trying to steal your data"
and a red cross through the HTTPS locl icon. This is because a certificate needs to
be recognized by a certificate authority to validate that you are indeed receiving
data from the organization you think you are, and not a MITM. This can't be fixed,
unless you were to buy a domain name and pass your self-signed certificate to a certificate
authority. Ignore the warnings.

## Transition a site to HTTP/2

### Set up a testing environment for enabling HTTP/2

We've created [a fork of VVV with HTTP/2 enabled](https://github.com/ericandrewlewis/VVV/tree/http2).
This is done by using nghttpx as an HTTP/2 proxy.

## What changes can be made in core to support HTTP/2 better?

The WP function [`is_ssl()`](https://github.com/WordPress/WordPress/blob/master/wp-includes/functions.php#L3748)
doesn't work properly and causes a redirect loop when serving the application through an HTTPS proxy
that runs the web server over HTTP.

Should concatenation of assets be modified for HTTP/2?

## Other notes

Placing WordPress behind an HTTPS proxy that requests WordPress over HTTP causes
WordPress to think it's running over HTTP (i.e. [`is_ssl()`](https://github.com/WordPress/WordPress/blob/master/wp-includes/functions.php#L3748) will return false).
Modifying `$_SERVER` variables in `wp-config.php` is the suggested configuration.
See trac tickets #9235, #19654, #31288.