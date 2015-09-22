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
Authenticity is verified with an **SSL certificate**.

A **certificate authority** (CA) is responsible for creating and issuing an SSL certificate.
The CA first validates that the person requesting a certificate owns the domain,
then issues a certificate. The certificate includes two important pieces of information:
a **digital signature** and the **web server's public key**.

The digital signature (along with the certificate chain) can be verified to prove
the CA created the certificate. Therefore, the information in the certificate can be trusted
([read more about how a digital signature works](about-public-key-cryptography.md#digital-signature)).
This is part of assuring authenticity.

The certificate also includes the web server's public key. If the digital signature
can be verified, we can trust the public key as well. The public key is used to encrypt
data between the user and the web server. Data encryption guarantees **privacy**, as
no passive attackers can see what's being said by either party.

[Intro to TLS](http://chimera.labs.oreilly.com/books/1230000000545/ch04.html)

[HTTPS Everywhere](https://www.youtube.com/watch?v=cBhZ6S0PFCY)

Convinced? Learn [how to setup HTTPS](https-setup-guide.md).