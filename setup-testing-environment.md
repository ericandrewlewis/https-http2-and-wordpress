## Set up a testing environment

Some may find that creating a testing environment for enabling HTTPS preferable to making changes on a production site.

To configure a site to run HTTPS in a development environment, an encryption key and self-signed TLS certificate is required. [This site](http://www.selfsignedcertificate.com/) will generate the `openssl` commands to run to create both.

[VVV](https://github.com/Varying-Vagrant-Vagrants/VVV), a popular development environment for WordPress, [creates a cert and encryption key](https://github.com/Varying-Vagrant-Vagrants/VVV/blob/v1.1/provision/provision.sh#L233-L246), although the default sites are [setup for HTTP](https://github.com/Varying-Vagrant-Vagrants/VVV/blob/v1.1/provision/provision.sh#L470).

Browsers will issue a warning that the certificate authority is invalid, because we signed the cert ourselves. To silence these warnings (and get the green lock in your browser), you should trust the certificate either [on your computer](https://support.apple.com/kb/PH10968?locale=en_US) or in your browser.