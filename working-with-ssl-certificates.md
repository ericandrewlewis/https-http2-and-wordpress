# Working with SSL certificates

To work with SSL certificates, install [openSSL](https://www.openssl.org/) from source or with your system's package manager.

## Converting

Certificates are typically not in human readable form.

They are typically encoded in either the DER format (binary), or PEM format (base64 encoded).

### From DER format to PEM

```bash
openssl x509 -in cert.crt -inform der -outform pem -out cert.pem
```

### View the human readable information in a PEM encoded certificate

```bash
openssl x509 -in cert.pem -text -noout
```

## Create a certificate

Create a private key

```bash
openssl genrsa -out server.key 2048
```

Create a self-signed certificate

```bash
openssl req -new -x509 -key server.key -out server.crt -days 3650
```

Create a certificate signing request

```bash
openssl req -out CSR.csr -key server.key -new
```


## Concatenating certificates

You may want to concatenate certificates into a single file, e.g. to verify a certificate is good. Note that only PEM encoded certificates can be concatenated.

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

Concatenate all PEM encoded intermediary certificates and the root certificate in `cacert.pem`.

You need a PEM encoded site certificate in `server.pem`.

```bash
openssl verify -verbose -CAfile cacert.pem server.pem
```

## External links

[Converting certificates from one form to another](https://support.ssl.com/Knowledgebase/Article/View/19/0/der-vs-crt-vs-cer-vs-pem-certificates-and-how-to-convert-them)