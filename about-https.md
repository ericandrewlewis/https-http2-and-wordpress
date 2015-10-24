# About HTTPS

## What is HTTPS?

HTTP is the protocol for transferring data on the internet. When requesting a webpage, a browser sends an HTTP request to a web server, and the web server returns the content in an HTTP response.

HTTP is insecure. A browser sends data **unencrypted** (in plaintext) through the internet to a web server over HTTP, **without authenticating** the server's identity. This makes users vulnerable to attack.

Users browsing websites over HTTP on an insecure wireless network are vulnerable. An attacker can eavesdrop on all communication between the user and the internet, allowing the attacker to collect metadata about the user's online behavior.

If a user logs into a website over HTTP, an attacker eavesdropping on the communication can steal the user's login credentials.

Since HTTP lacks server authentication, an attacker performing a [man-in-the-middle attack](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) can act as a website (e.g. your bank), interacting with a user to steal their personal information.

HTTPS is a secure version of HTTP that addresses these security problems, adding **encryption** and **server identification** to the HTTP data transfer model.

## What is HTTPS not?

HTTPS provides security of **server identification** and **encryption** for data transfer. It **does not** make your website immune to other security vulnerabilities. If a website allows user login, a hacker can still attempt to crack user accounts with a brute force attack, over HTTP or HTTPS.

## Why setup HTTPS?

HTTPS is the way forward for the web. Various organizations have decided that privacy by default is the future, and will actively work towards that goal.

* SEO. [Google gives HTTPS sites a ranking boost](http://googlewebmastercentral.blogspot.com/2014/08/https-as-ranking-signal.html).
* Data encryption security. Middle-men attackers cannot listen to conversations.
* Server identification. Middle-men attackers cannot pretend to be the server we want to communicate with.
* New browser features are limited to HTTPS sites. Browsers will soon deprecate features for HTTP sites ([1](https://blog.mozilla.org/security/2015/04/30/deprecating-non-secure-http/), [2](https://www.chromium.org/Home/chromium-security/marking-http-as-non-secure)).

## How does HTTPS work?

HTTPS provides certain security features: **server identification** and **encryption**.

**Server identification** ensures that the website a user connects to is the website the user *thinks* they're connecting to, and not a "man-in-the-middle" attacker acting as the website. Server identification is verified with an **SSL certificate**.

A **certificate authority** (CA) is responsible for creating and issuing an SSL certificate. The CA first validates that the person requesting a certificate owns the domain, then issues a certificate.

When a user first connects to a website over HTTPS, the server returns an SSL certificate. A certificate includes two important pieces of information: a **digital signature** and the **web server's public key**.

The digital signature (along with the certificate chain) proves the CA created the certificate, which means the certificate can be trusted. [Learn how a digital signature works](about-public-key-cryptography.md#digital-signature).

The certificate also includes the web server's public key. If the digital signature is verified, the public key can be trusted as well. The public key is used in encrypting data between the user and the web server. Although attackers might be able to get an encrypted message, they can't decode it. [Learn how public key cryptography works](https://github.com/ericandrewlewis/https-http2-and-wordpress/blob/master/about-public-key-cryptography.md#encrypting-plaintext).

Convinced? Learn [how to setup HTTPS](https-setup-guide.md).

## External Links

[Intro to TLS](http://chimera.labs.oreilly.com/books/1230000000545/ch04.html)

[HTTPS Everywhere](https://www.youtube.com/watch?v=cBhZ6S0PFCY)

[How does HTTPS actually work?](http://robertheaton.com/2014/03/27/how-does-https-actually-work/)

[IndieWebCamp HTTPS guide](http://indiewebcamp.com/https)

Convinced? Learn [how to setup HTTPS](https-setup-guide.md).

## Stray questions

An EV certificate adds the company's name to the address bar. [see here](https://www.globalsign.com/en/ssl-information-center/what-is-an-extended-validation-certificate/). What reasons would people want an EV cert?

Yes, you can add multiple domain names to one certificate.