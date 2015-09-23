# About HTTPS

## What is HTTPS?

HTTP is the protocol for transferring data on the internet. A browser
sends an HTTP request to a web server for the content of a webpage, and the web server
returns the content in an HTTP response.

HTTP is insecure. Any data a browser sends to a web server over HTTP is in plaintext.
Anyone connected to a wireless network can look at data being transferred over HTTP by
anyone else on the network. This has security implications (user account data), but
also personal information. Just the fact that you're visiting certain websites
paints a portrait.

On top of that, connections are not authenticated. Websites served over HTTP can be spoofed by attackers.

HTTPS is a secure version of HTTP that addresses these security problems, providing
**encryption** and **authenticity** while transferring data. For example, if a user submits
their credit card information on a website to buy a product,
the data must be sent over HTTPS.

## What is HTTPS not?

HTTPS provides security of **authenticity** and **encryption** for data transfer.
It **does not** make your website immune to other security vulnerabilities.
If a website has a user login protected section, a hacker can still attempt to
break into it with brute force, over HTTP or HTTPS.

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

## How does HTTPS work?

HTTPS provides certain security features: **authenticity** and **encryption**.

**Authenticity** ensures that the website we're connecting to is the website we *think* we're
connecting to, and not a malicious "man-in-the-middle" pretending to be the website.
Authenticity is verified with an **SSL certificate**.

A **certificate authority** (CA) is responsible for creating and issuing an SSL certificate.
The CA first validates that the person requesting a certificate owns the domain,
then issues a certificate. A certificate includes two important pieces of information:
a **digital signature** and the **web server's public key**.

The digital signature (along with the certificate chain) proves the CA created the certificate,
which means the certificate can be trusted. [Learn how a digital signature works](about-public-key-cryptography.md#digital-signature).

The certificate also includes the web server's public key. If the digital signature
is verified, the public key as well. The public key is used to encrypt
data between the user and the web server. Although attackers can the encrypted messages,
they can't see the decrypted plain text.

[Intro to TLS](http://chimera.labs.oreilly.com/books/1230000000545/ch04.html)

[HTTPS Everywhere](https://www.youtube.com/watch?v=cBhZ6S0PFCY)

Convinced? Learn [how to setup HTTPS](https-setup-guide.md).