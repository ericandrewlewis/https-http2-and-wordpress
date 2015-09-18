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

A web server that talks HTTPS will have a few files required for HTTPS.

A **private key**.

An **SSL certificate**, which includes a **public key**.

[Intro to TLS](http://chimera.labs.oreilly.com/books/1230000000545/ch04.html)

Convinced? Learn [how to setup HTTPS](https-setup-guide.md).