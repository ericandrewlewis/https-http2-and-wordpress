# HTTPS Setup Guide

## Change all embedded content loaded over HTTP to HTTPS

Zack Tollman's [HTTPS Mixed Content Detector](https://www.tollmanz.com/wordpress-https-mixed-content-detector/)
will log all embedded content loaded over HTTP.

## Set up a testing environment for enabling HTTPS

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

## Get encryption key and certificate

You need an encryption key and a certificate on your website's server to provide HTTPS.

Check if your web host permits you to submit your own key and certificate. If
not, they are coercing you to purchase security through them, which you will have to do.

If your web host allows you to submit your own key and certificate, you can
create a private key and get a certificate from any certificate authority.

If you don't mind paying $16 per year per hostname, [sslmate](https://sslmate.com/) is
great.

If you really don't want to pay, [startssl](https://startssl.com) offers free certificates.

Starting November 2015, [Let's Encrypt](letsencrypt.com) will offer free certificates
with a command line utility.

## Existing Guides

[User handbook](https://make.wordpress.org/support/user-manual/web-publishing/https-for-wordpress/)

[WP Beginner](http://www.wpbeginner.com/wp-tutorials/how-to-add-ssl-and-https-in-wordpress/)