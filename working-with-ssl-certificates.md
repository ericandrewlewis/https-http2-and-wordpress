# Working with SSL certificates

Certificates are typically not in human readable form.

They are typically encoded in either the DER format (binary), or base64 encoded.

[Converting certificates from one form to another](https://support.ssl.com/Knowledgebase/Article/View/19/0/der-vs-crt-vs-cer-vs-pem-certificates-and-how-to-convert-them)

How to certify an SSL certificate with the intermediary chain?

## Convert DER format to base64

```bash
openssl x509 -in cert.crt -inform der -outform pem -out cert.pem
```

## View base64 encoded certificate

```bash
openssl x509 -in cert.pem -text -noout
```

## Concatenating certificates

You may want to concatenate certificates into a single file, e.g. to verify a
certificate is good.

Only base64 encoded certificates can be concatenated, so convert as appropriate
and concatenate them together.

e.g.

```
-----BEGIN CERTIFICATE-----
MIIEKjCCAxKgAwIBAgIEOGPe+DANBgkqhkiG9w0BAQUFADCBtDEUMBIGA1UEChML
RW50cnVzdC5uZXQxQDA+BgNVBAsUN3d3dy5lbnRydXN0Lm5ldC9DUFNfMjA0OCBp
...
bYQLCIt+jerXmCHG8+c8eS9enNFMFY3h7CI3zJpDC5fcgJCNs2ebb0gIFVbPv/Er
fF6adulZkMV8gzURZVE=
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
zX1XEC+bBAlahLVu2B064dae0Wx5XnkcFMXj0EyTO2U87d89vqbllRrDtRnDvV5b
u/8j72gZyxKTJ1wDLW8w0B62GqzeWvfRqqgnpv55gcR5mTNXuhKwqeBCbJPKVt7+
...
bYQLCIt+jerXmCHG8+c8eS9enNFMFY3h7CI3zJpDC5fcgJCNs2ebb0gIFVbPv/Er
fF6adulZkMV8gzURZVE=
-----END CERTIFICATE-----
```

## Validate a certificate

Include all intermediary certificates in `server.crt`
```bash
openssl verify -verbose -CAfile cacert.pem  server.crt
```
