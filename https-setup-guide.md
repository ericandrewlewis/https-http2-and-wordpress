# How to setup HTTPS

## Create a private key and SSL certificate

A web server needs a private key and an SSL certificate (which includes a public key) to serve HTTPS.

If the site is hosted by a web hosting provider, look for the host's documentation on how to configure HTTPS. Any good host will allow users to purchase an SSL certificate from a provider of their choosing.

[sslmate](https://sslmate.com/) automates the SSL key and certificate process, for a price.

Starting in November 2015, [Let's Encrypt](https://letsencrypt.com), a new certificate authority, will offer free certificates and key configuration with a command line utility.

[Start SSL](https://startssl.com) offers free certificates. [Here is a guide](https://konklone.com/post/switch-to-https-now-for-free).

## Configure the site to serve both HTTP and HTTPS

Serve content over both HTTP and HTTPS. You should never stop serving HTTP, although later steps should be taken to send traffic to the HTTPS version of content with the appropriate means.

Hosting providers may have administrative interfaces to enable HTTPS.

If you manage the web server configuration, Mozilla has an [HTTPS configuration generator](https://mozilla.github.io/server-side-tls/ssl-config-generator/) which produces boilerplate for configuring various web servers to server HTTPS.

## Finding existing embedded HTTP content

Embedded HTTP content in webpages (images, javascript files, stylesheets) may be hardcoded in a file or in database content (e.g. `<img src="http://example.com/image.jpg">`). These references should be changed to load over `https://`. The secure version is always preferred, as [the protocol relative URL is an anti-pattern](http://www.paulirish.com/2010/the-protocol-relative-url/).

Reading the raw HTML output or scanning database content may not find all assets. Content loaded asynchronously will not be found.

Content Security Policy Report Only, a contortion of [Content Security Policy (CSP)](http://www.html5rocks.com/en/tutorials/security/content-security-policy/), can be used to build a list of embedded HTTP content. CSP is an HTTP header which defines an embedded content loading policy that a web browser should respect. For example, a policy could dictate that the browser should load image assets only over a specific CDN host, and the browser would block image assets from other hosts.

Content Security Policy Report Only operates similarly, but instead of block assets, the browser serves them to the user while silently sending a report of the policy violations to a pre-defined URL. This allows you to collect embedded insecure content into a list.

Here's an example of a CSP Report Only, assuming you have implemented an endpoint that will handle CSP Report-Only requests.

```
Content-Security-Policy-Report-Only: default-src https: data: 'unsafe-inline' 'unsafe-eval'; child-src https:; connect-src https:; font-src https: data:; img-src https: data:; media-src https:; object-src https:; script-src https: 'unsafe-inline' 'unsafe-eval'; style-src https: 'unsafe-inline'; report-uri http://yourwebsite.com/csp-report-only-endpoint;
```

If the site is a WordPress site, the [HTTPS Mixed Content Detector](https://www.tollmanz.com/wordpress-https-mixed-content-detector/) plugin can be used to collect embedded HTTTP content. It implements CSP-Report-Only as you browse the site as an admin. Violation reports are stored in the admin interface.

## Transition embedded content to load over HTTPS

Work through the list of insecure embedded assets. Make sure a secure version of the embedded content is available, and modify it to load over HTTPS.

*What if a user is embedding an image from another website that doesn't serve HTTPS? What are the intellectual property issues at play if they host the image themselves?*

## Verify latest packages

Verify openssl and kernel are latest on server handling TLS for perf optimizations.

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

## Does your server need a unique IP?

If you want to avoid security warnings in some older browsers (e.g. IE 8 Windows XP and similar), you will need a unique IP for your certificate for clients that don't have [SNI support](https://en.wikipedia.org/wiki/Server_Name_Indication).

Enable session resumption and false start.

Optimize TLS record size to avoid overflowing a TCP congestion window ([1](https://youtu.be/fQX2mJ11vCs?t=1834))

OCSP stapling

Terminate TLS as close to the user as possible for dynamic requests. I.e. use CDNs for dynamic requests?

## Force users to connect to your site via HTTPS

HTTP Strict Transport Security forces users to always connect to your site securely.

This is a preferred alternative to insecurely redirecting HTTP requests to HTTPS via a 301 response.

[Read more about HSTS](https://https.cio.gov/hsts/).

HSTS preload list https://hstspreload.appspot.com/

## SEO-changes

"Fetch as Google" inside of webmaster tools may be helpful here.

## Change WordPress site settings

In the WordPress admin under General > Settings, change the site url and home url
to `https`.

## Checking security quality

SSL Labs offers [a free report](http://www.dh-test-ssl.com), which will grade your TLS implementation.

## Links

[HTTPS for WordPress in the User handbook](https://make.wordpress.org/support/user-manual/web-publishing/https-for-wordpress/)

[WP Beginner's HTTPS setup guide](http://www.wpbeginner.com/wp-tutorials/how-to-add-ssl-and-https-in-wordpress/)

[The Federal Government's HTTPS Everywhere information website](https://https.cio.gov/). Lot of good stuff.

[Testing with Open SSL](https://www.feistyduck.com/library/openssl-cookbook/online/ch-testing-with-openssl.html). Lot of good commands.

[Mozilla — Server Side TLS](https://wiki.mozilla.org/Security/Server_Side_TLS)

## Other notes

Placing WordPress behind an HTTPS proxy that requests WordPress over HTTP causes WordPress to think it's running over HTTP (i.e. [`is_ssl()`](https://github.com/WordPress/WordPress/blob/master/wp-includes/functions.php#L3748) will return false). Modifying `$_SERVER` variables in `wp-config.php` is the suggested configuration. See trac tickets #9235, #19654, #31288.