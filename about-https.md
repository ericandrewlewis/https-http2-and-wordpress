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

* SEO. [Google gives HTTPS sites a ranking boost](http://googlewebmastercentral.blogspot.com/2014/08/https-as-ranking-signal.html).
* User login security. If you're logging into your site, your WordPress user credentials
should be sent securely to avoid anyone stealing your credentials.
* Browser features. New browser features are limited to HTTPS sites and soon will deprecate features for HTTP sites ([1](https://blog.mozilla.org/security/2015/04/30/deprecating-non-secure-http/), [2](https://www.chromium.org/Home/chromium-security/marking-http-as-non-secure)).
* User privacy. Passive attackers listening to a user's web browsing activity can build up a portrait
of what a user is doing on the internet. Only with ubiquitous secure web browsing can
we avoid this.

## What is HTTPS not?

HTTPS provides security of **authenticity**, **privacy** and **integrity**, mainly
dealing with securing communication between a client and a server. It **does not**
make your web server immune to other types of vulnerabilities, such as brute force
cracking into a user account protected part of a website.

## How does HTTPS work?

HTTPS provides certain security features: **authenticity**, **privacy** and **integrity**.

**Authenticity** ensures that the website we're connecting to is the website we *think* we're
connecting to, and not a malicious "man-in-the-middle" pretending to be the website.

An **SSL certificate** provides this authenticity.

A **certificate authority** (CA) is responsible for creating SSL certificates.
A CA validates domain ownership, typically through an email related to the domain's
WHOIS information. Then the certificate is created. The certificate includes two
important piece of information: the signature and the public key.

The signature is created with the private key of the CA. The private key of the CA
is a well-guarded secret. By signing the certificate, the CA is giving its stamp
of approval. Your browser can verify the authenticity of the SSL certificate by
looking at the CAs SSL certificate, and unscrambling the signature with its public key.
This goes certificate verification goes all the way to "root" certificates that
are installed on your computer.
[How Certificates Use Digital Signatures](http://commandlinefanatic.com/cgi-bin/showarticle.cgi?article=art012)
goes into detail about verifying SSL certficate chains by hand.

The other piece of information that's important in the site's SSL certificate is
the public key. Since the CA's signature has approved of the entire SSL certificate,
we can trust the rest of the information embedded, which includes the server's public key.
The server's public key is used to encrypt data when first negotiating an HTTPS connection.
This guarantees **privacy**, as data is encrypted between the user and the website.

If a man-in-the-middle attempts to spoof a website, it could provide a certificate,
but without the server's private key that matches the public key, it would fail HTTPS negotiation,
and not be able to complete the connection.

[Intro to TLS](http://chimera.labs.oreilly.com/books/1230000000545/ch04.html)

[HTTPS Everywhere](https://www.youtube.com/watch?v=cBhZ6S0PFCY)

Convinced? Learn [how to setup HTTPS](https-setup-guide.md).