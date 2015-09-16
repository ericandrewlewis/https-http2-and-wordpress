# Learn more and setting up HTTPS

## What is HTTPS?

HTTP is the protocol for transferring data on the internet. For example, a browser
sends an HTTP request to a web server for the content of a webpage, and the web server
sends an HTTP response.

HTTP is insecure. Any data the browser sends to the web server is out in the open.
Anyone connected to a wireless network can look at the data being transferred by
anyone else on the network in plain text.

HTTPS is a secure version of HTTP. A user submitting their credit card information
on a website to buy something would send the data over HTTPS to the web server.

## Why setup HTTPS?

HTTPS is the way forward for the web.

* [Google gives HTTPS sites an SEO ranking boost](http://googlewebmastercentral.blogspot.com/2014/08/https-as-ranking-signal.html).
* **Security**. If you're logging into your site, your WordPress user credentials
should be sent securely to avoid anyone stealing your credentials.
* Browsers are limiting new features to HTTPS sites and soon will deprecate features for HTTP sites ([1](https://blog.mozilla.org/security/2015/04/30/deprecating-non-secure-http/), [2](https://www.chromium.org/Home/chromium-security/marking-http-as-non-secure)).

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

## Change all embedded content loaded over HTTP to HTTPS

Install the [HTTPS Mixed Content Detector](https://www.tollmanz.com/wordpress-https-mixed-content-detector/) plugin.
It will log content served over HTTP as you browse your site while logged in as an admin.
The logs are stored in the admin interface under Content Security Policy Reports.
Change all these to load over HTTPS.

*What if a user is embedding an image from another website and they don't serve HTTPS?
What are the intellectual property issues at play if they host the image themselves?

## Does your server need a unique IP?

If you want to avoid security warnings in some older browsers (i.e. anything on Windows XP),
you will need a unique IP for your certificate for clients that don't support [SNI](https://en.wikipedia.org/wiki/Server_Name_Indication).

## Get encryption key and certificate

You need an encryption key and a certificate on your website's server to provide HTTPS.

Check if your web host permits you to submit your own key and certificate. If
not, they are coercing you to purchase security through them, which you will have to do.

If your web host allows you to submit your own key and certificate, you can
create a private key and get a certificate from any certificate authority.

If you don't mind paying $16 per year per hostname, [sslmate](https://sslmate.com/) is
great.

If you really don't want to pay, [startssl](https://startssl.com) offers free certificates.
[Here is a guide](https://konklone.com/post/switch-to-https-now-for-free).

Starting November 2015, [Let's Encrypt](letsencrypt.com) will offer free certificates
with a command line utility.

## Change WordPress site settings

In the WordPress admin under General > Settings, change the site url and home url
to `https`.

## Checking security quality

SSL Labs offers [a free report](http://www.dh-test-ssl.com), which will grade
your TLS implementation.

## Existing Guides

[User handbook](https://make.wordpress.org/support/user-manual/web-publishing/https-for-wordpress/)

[WP Beginner](http://www.wpbeginner.com/wp-tutorials/how-to-add-ssl-and-https-in-wordpress/)