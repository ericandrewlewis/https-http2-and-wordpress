## HTTPS Setup Guide

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