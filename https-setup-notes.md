# HTTPS setup notes

## Dreamhost

I registered for Shared Hosting and got a free domain registration for dh-test-ssl.com.

I cannot get an SSL site at time of purchase.

Do I need a Unique IP? If I'm on a shared host that uses multiple domain names on
the same IP, then older browsers (e.g. users on Windows XP) will display a security warning
about the certificate.

I can add a self-signed certificate manually through "Secure Hosting." I can manually
edit SSL cert stuff.

So I'll try making a certificate from startssl.com. I go to the fastlane thing.

I have strong privacy on, so StartSSL can't figure out my personal email to send it to.
I made my info non-private so hopefully startssl figures out the right email for
auth.

Zack said we could link to this for how to use startssl.com https://konklone.com/post/switch-to-https-now-for-free

What keysize should I pick? Seems like