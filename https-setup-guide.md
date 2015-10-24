# How to setup HTTPS

## Create a private key and SSL certificate

A web server needs a private key and an SSL certificate (which includes a public key) to serve HTTPS.

If the site is served by a web hosting provider, look for the host's documentation on how to configure HTTPS. Any good host will allow users to purchase an SSL certificate from a provider of their choosing.

[sslmate](https://sslmate.com/) automates the SSL key and certificate process, for a price.

Starting in November 2015, [Let's Encrypt](https://letsencrypt.com), a new certificate authority, will offer free certificates and key configuration with a command line utility.

[Start SSL](https://startssl.com) offers free certificates. [Here is a guide](https://konklone.com/post/switch-to-https-now-for-free).

## Configure the site to serve both HTTP and HTTPS

Serve content over both HTTP and HTTPS. You should never stop serving HTTP, although later steps should be taken to send traffic to the HTTPS version of content with the appropriate means.

Hosting providers may have administrative interfaces to enable HTTPS.

If you manage the web server configuration, Mozilla has an [HTTPS configuration generator](https://mozilla.github.io/server-side-tls/ssl-config-generator/) which produces boilerplate for configuring various web servers to server HTTPS.

## Find insecure embedded content

Insecure embedded content on a website (images, javascript files, stylesheets) may be hardcoded in a file or in database content (e.g. `<img src="http://example.com/image.jpg">`). These references should be changed to load over `https://`. The secure version is always preferred, and [the protocol relative URL is an anti-pattern](http://www.paulirish.com/2010/the-protocol-relative-url/).

Scanning raw HTML output or database content may not find all assets. Content loaded asynchronously will be missed.

`Content-Security-Policy-Report-Only`, a relative of [Content Security Policy (CSP)](http://www.html5rocks.com/en/tutorials/security/content-security-policy/), can be used to find insecure embedded content. CSP is an HTTP header which defines an embedded content loading policy that a web browser should respect. For example, a policy could dictate that the browser should load image assets only over a specific CDN host, and the browser would block image assets from other hosts.

Content-Security-Policy-Report-Only operates in a similar fashion. Instead of block assets, the browser serves them to the user while silently sending a report of the policy violations to a pre-defined URL. This allows you to collect insecure embedded content into a list.

Here's an example of a CSP Report-Only header, assuming you have implemented an endpoint that will handle the report requests.

```
Content-Security-Policy-Report-Only: default-src https: data: 'unsafe-inline' 'unsafe-eval'; child-src https:; connect-src https:; font-src https: data:; img-src https: data:; media-src https:; object-src https:; script-src https: 'unsafe-inline' 'unsafe-eval'; style-src https: 'unsafe-inline'; report-uri http://yourwebsite.com/csp-report-handler;
```

If the site is a WordPress site, the [HTTPS Mixed Content Detector](https://www.tollmanz.com/wordpress-https-mixed-content-detector/) plugin can be used to collect insecure embedded content. It implements CSP-Report-Only as you browse the site as an admin. Violation reports are stored in the admin interface.

## Transition insecure embedded content to load over HTTPS

Work through the list of insecure embedded content. Check that a secure version of the embedded content is available, and modify it to load over HTTPS.

*What if a user is embedding an image from another website that doesn't serve HTTPS? What are the intellectual property issues at play if they host the image themselves?*

## Canonical tags

Use [canonical tags](https://support.google.com/webmasters/answer/139066?hl=en) to always use the `https://` version of a webpage.

## Force users to connect to your site via HTTPS

HTTP Strict Transport Security is a mechanism to force users to connect to your site securely, and avoid security downgrade attacks. [Read more about HSTS and how to implement](https://https.cio.gov/hsts/).

## Add 301 redirects from HTTP to HTTPS

Older clients don't support HSTS, so always 301 redirect HTTP requests to HTTPS.

## Verify latest server packages

The web server's system kernel and openssl packages should be updated to the latest for performance optimizations.

## Does your server need a unique IP?

If you want to avoid security warnings in some older browsers (e.g. IE 8 Windows XP and similar), you will need a unique IP for your certificate for clients that don't have [SNI support](https://en.wikipedia.org/wiki/Server_Name_Indication).

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

[Speeding up SSL: enabling session reuse](http://vincent.bernat.im/en/blog/2011-ssl-session-reuse-rfc5077.html)

## Other notes

Placing WordPress behind an HTTPS proxy that requests WordPress over HTTP causes WordPress to think it's running over HTTP (i.e. [`is_ssl()`](https://github.com/WordPress/WordPress/blob/master/wp-includes/functions.php#L3748) will return false). Modifying `$_SERVER` variables in `wp-config.php` is the suggested configuration. See trac tickets #9235, #19654, #31288.