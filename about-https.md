# About HTTPS

## Overview

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

## How does HTTPS work?

HTTPS provides security in separate features: **authenticity**, **privacy* and **integrity**.

Why do we need to authenticate? Because anyone along the internet could spoof to be
the website you're trying to communicate with. For this reason, we need to be able
to ensure that the server communicating with us can reasonably identify itself.

Authenticity is made possible through an SSL certificate, which a server provides
and somehow proves that this exact server is the server it says it is. Certificates
are issued by root certificates.

What's to stop a hacker from installing a certificate on their intermediary server?

So SSL certificates are verified just by the fact that their public key is guaranteed,
and perhaps expiration information in the certificate? Right, the lack of the private
key means that an attacker cannot complete the TLS handshake, thereby affirming
authentication of the server.

So if I have your private key,
I can use your SSL certificate and run with it?

Root certificates are trusted by your computer. These root CAs sign all other
certs that you will trust.

Why do we need privacy? Because HTTP is out in the open.

A web server that talks HTTPS will have a few files required for HTTPS.

A **private key**.

An **SSL certificate**, which includes a **public key**.

[Intro to TLS](http://chimera.labs.oreilly.com/books/1230000000545/ch04.html)

Convinced? Learn [how to setup HTTPS](https-setup-guide.md).