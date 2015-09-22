# About Public Key Cryptography

[Public key cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography)
is a means of either encrypting plaintext or creating a digital signature.

## Encrypting plaintext

A sender encrypts plaintext to send to someone with the receiver's public key.
The sender can then send the encrypted text to the receiver, who can unencrypt
the text with their private key.

Encrypting plaintext is used during the TLS handshake between a client and a
web server.

## Digital signature

Someone can create a digital signature by creating an encrypted string with
their private key. Other people can verify the signature by decrypting the string
with the signer's public key. The fact that only the signer has their private key
means that only they could have signed the string.

Digital signatures are used in SSL certificates to prove a certificate is blessed
by a certificate authority.

### How an SSL certificate is verified

Your browser can verify the authenticity of the SSL certificate by looking at the CAs SSL certificate,
and unscrambling the signature with its public key. This goes certificate verification
goes all the way to "root" certificates that are installed on your computer.
[How Certificates Use Digital Signatures](http://commandlinefanatic.com/cgi-bin/showarticle.cgi?article=art012)
goes into detail about verifying SSL certficate chains by hand.
