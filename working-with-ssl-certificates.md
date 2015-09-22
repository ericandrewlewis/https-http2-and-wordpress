# Working with SSL certificates

Certificates are typically not in human readable form.

They are often stored encoded in the DER format, which is binary.

[Converting certificates from one form to another](https://support.ssl.com/Knowledgebase/Article/View/19/0/der-vs-crt-vs-cer-vs-pem-certificates-and-how-to-convert-them)

Convert DER to PEM

```bash
openssl x509 -in cert.crt -inform der -outform pem -out cert.pem
```

View PEM encoded certificate

```bash
openssl x509 -in cert.pem -text -noout
```