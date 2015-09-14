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

sslmate is a much smoother experience but costs $15.

What keysize should I pick? Tollman drafted an article about this https://github.com/tollmanz/tollmanz.com/blob/tls-article/_posts/2015-03-15-decision-based-tls-deployment.markdown
RSA itself recommends a keysize of 2048 until 2030 (in an article linked by Zack)

I'm creating a key password that will encrypt the key.

I save the key to my computer.

I changed my mind and decide to create a key on my computer with openssl