---
title: >-
  A brief explanation of creating an OpenSSL Certificate Authority (CA) and
  certificates (certs)
date: 2025-04-23 12:53:43
tags:
- software
- english
---
Using our own certificate authority is useful for the security of the private networks we manage. The learning curve of this process is also benefical for understanding how certificates work, though I'll not dive into details in this writing.

I'll briefly introduce the steps to create an CA and certs with OpenSSL. No confusing details included, just what you need to go! Let's get straight into it...

## Create and Setup a CA

  1. **Create a private key for the CA:** ``openssl genrsa -des3 -out myCA.key 2048``
  2. **Create the CA with the private key:** 
``openssl req -x509 -new -nodes -key myCA.key -sha256 -days 1825 -out myCA.pem``

CA is successfully created! You can add the **myCA.pem** into your trusted root certificates.

## Create SSL certificates for websites with the created CA

  1. **Create private key for the website:**
``openssl genrsa -out mywebsite.com.key 2048``
  2. **Create signing event:**
``openssl req -new -key mywebsite.com.key -out mywebsite.com.csr``
  3. **Create X509 V3 certificate extension config:** mywebsite.ext >
```
    authorityKeyIdentifier=keyid,issuer
    basicConstraints=CA:FALSE
    keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
    subjectAltName = @alt_names

    [alt_names]
    DNS.1 = mywebsite.com
```
  4. **Create SSL certificate files for the website:**
``openssl x509 -req -in mywebsite.com.csr -CA myCA.pem -CAkey myCA.key \
-CAcreateserial -out mywebsite.com.crt -days 825 -sha256 -extfile mywebsite.com.ext``

_Voila_! **mywebsite.com.crt** and **mywebsite.com.key** are ready to be used :)

_Note: "mywebsite.com" is a placeholder. Change it to a website address you want to create certificate for._
