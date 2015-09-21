# About HTTPS

## What is HTTPS?

HTTP is the protocol for transferring data on the internet. A browser
sends an HTTP request to a web server for the content of a webpage, and the web server
returns the content in an HTTP response.

HTTP is insecure. Any data a browser sends to a web server over HTTP is out in the open.
Anyone connected to a wireless network can look at data being transferred over HTTP by
anyone else on the network.

HTTPS is a secure version of HTTP. A user submitting their credit card information
on a website to buy something would send the data over HTTPS to the web server.

## Why setup HTTPS?

HTTPS is the way forward for the web. Various organizations have decided that privacy
by default is the future, and will actively work towards that goal.

* [Google gives HTTPS sites an SEO ranking boost](http://googlewebmastercentral.blogspot.com/2014/08/https-as-ranking-signal.html).
* **Security**. If you're logging into your site, your WordPress user credentials
should be sent securely to avoid anyone stealing your credentials.
* Browsers are limiting new features to HTTPS sites and soon will deprecate features for HTTP sites ([1](https://blog.mozilla.org/security/2015/04/30/deprecating-non-secure-http/), [2](https://www.chromium.org/Home/chromium-security/marking-http-as-non-secure)).
* Passive attackers listening to a user's web browsing activity can build up a portrait
of what a user is doing on the internet. Only with ubiquitous secure web browsing can
we avoid this.

## What is HTTPS not?

HTTPS provides security of **authenticity**, **privacy** and **integrity**, mainly
dealing with securing communication between a client and a server. It **does not**
make your web server immune to other types of vulnerabilities, such as brute force
cracking into a user account protected part of a website.

## How does HTTPS work?

HTTPS provides certain security features: **authenticity**, **privacy** and **integrity**.

Why do we need to authenticate? Because anyone along the internet could spoof to be
the website you're trying to communicate with. For this reason, we need to be able
to ensure that the server communicating with us can reasonably identify itself.

Authenticity is made possible through an **SSL certificate**. An SSL certificate is
a file cryptographically signed by a certificate authority that ensures the public key
of a server, along with other information (expiration date, signer, etc.).

A browser will verify the certificate chain of an SSL certificate to ensure that one
of the root certificates that the browser of the user's computer trusts is a parent
of the SSL certificate provided by a website. In this manner, the chain of trust
backs a public key of the server. If the server provides an SSL certificate with a
public key, but does not have the private key, it will not be able to complete the
TLS handshake.

Why do we need privacy? Because HTTP is out in the open.

A web server that talks HTTPS will have a few files required for HTTPS.

[Intro to TLS](http://chimera.labs.oreilly.com/books/1230000000545/ch04.html)

[HTTPS Everywhere](https://www.youtube.com/watch?v=cBhZ6S0PFCY)

Convinced? Learn [how to setup HTTPS](https-setup-guide.md).