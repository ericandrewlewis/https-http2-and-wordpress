# HTTPS Setup guide

## Set up a testing environment for enabling HTTPS

Some may find that creating a testing environment for enabling HTTPS preferable to
making changes on a production site.

To configure a site to run HTTPS in a development environment, an encryption key
and self-signed TLS certificate is required. [This site](http://www.selfsignedcertificate.com/)
will generate the `openssl` commands to run to create both.

[VVV](https://github.com/Varying-Vagrant-Vagrants/VVV), a popular development
environment for WordPress, [creates a cert and encryption key](https://github.com/Varying-Vagrant-Vagrants/VVV/blob/v1.1/provision/provision.sh#L233-L246),
although the default sites are [setup for HTTP](https://github.com/Varying-Vagrant-Vagrants/VVV/blob/v1.1/provision/provision.sh#L470).

Browsers will issue a warning that the certificate authority is invalid, because
we signed the cert ourselves. To silence these warnings (and get the green lock in
your browser), you should accept the certificate either [on your computer](https://support.apple.com/kb/PH10968?locale=en_US)
or in your browser.

## Change all embedded content to load over HTTPS

Change all embedded content in your site to load securely over HTTPS. Anywhere there's
an explicit reference to `http://` in a database or script file should be modified
to load over `https://`. As HTTPS should always be preferred, [the protocol relative URL is now an anti-pattern](http://www.paulirish.com/2010/the-protocol-relative-url/).

You'll need to find a way to get a list of all the insecure content loading on your
site. Content-Security-Policy-Report-Only (CSP-RO) allows you to stipulate an embedded content loading
policy. For our purposes, this will be to require HTTPS. Reading the raw HTML output
of your website is insufficient, because any asynchronously loaded content will be
overlooked. CSP-RO will catch all of this, because it is implemented at the browser-level.

A report of any embedded content violating the policy will be sent to a URL endpoint
of your choice. This allows you to collate all your embedded insecure content into a
list of things to change.

The [HTTPS Mixed Content Detector](https://www.tollmanz.com/wordpress-https-mixed-content-detector/) plugin
implements CSP-RO as you browse the site as an admin. The reports are stored in
the admin interface under Content Security Policy Reports.

*What if a user is embedding an image from another website and they don't serve HTTPS?
What are the intellectual property issues at play if they host the image themselves?

## Benchmark crypto?

eg. openssl speed sha ecdh`

## Does your server need a unique IP?

If you want to avoid security warnings in some older browsers (e.g. IE 8 Windows XP and similar),
you will need a unique IP for your certificate for clients that don't have [SNI support](https://en.wikipedia.org/wiki/Server_Name_Indication).

## Get a private key and SSL certificate

You need a private key and an SSL certificate on your website's server to provide HTTPS.

Check if your web host permits you to submit your own key and certificate. If they do,
you can create a private key and get a certificate from any certificate authority.

If you don't mind paying $16 per year per hostname, [sslmate](https://sslmate.com/) is
a great way to purchase SSL certificates.

If you really don't want to pay, [startssl](https://startssl.com) offers free certificates.
[Here is a guide](https://konklone.com/post/switch-to-https-now-for-free).

Starting November 2015, [Let's Encrypt](letsencrypt.com) will offer free certificates
with a command line utility.

## Tuning TLS

Enable session resumption and false start.

## Force users to connect to your site via HTTPS

HTTP Strict Transport Security forces users to always connect to your site securely.

This is a preferred alternative to insecurely redirecting HTTP requests to HTTPS
via a 301 response.

[Read more about HSTS](https://https.cio.gov/hsts/).

## SEO-changes

"Fetch as Google" inside of webmaster tools may be helpful here.

## Change WordPress site settings

In the WordPress admin under General > Settings, change the site url and home url
to `https`.

## Checking security quality

SSL Labs offers [a free report](http://www.dh-test-ssl.com), which will grade
your TLS implementation.

## Existing Guides

[User handbook](https://make.wordpress.org/support/user-manual/web-publishing/https-for-wordpress/)

[WP Beginner](http://www.wpbeginner.com/wp-tutorials/how-to-add-ssl-and-https-in-wordpress/)

[The Federal Government put a helpful website together](https://https.cio.gov/)

## Other notes

Placing WordPress behind an HTTPS proxy that requests WordPress over HTTP causes
WordPress to think it's running over HTTP (i.e. [`is_ssl()`](https://github.com/WordPress/WordPress/blob/master/wp-includes/functions.php#L3748) will return false).
Modifying `$_SERVER` variables in `wp-config.php` is the suggested configuration.
See trac tickets #9235, #19654, #31288.