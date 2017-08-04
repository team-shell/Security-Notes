# Client Cert Authentication
## Overview
Client certificates as the name indicates are used to identify a client or a user. They are meant for authenticating the client to the server. In case of a client certificate the value of this field would be set to a users name.

A client digital certificate or client certificate is basically a file, usually protected with a password and loaded unto a client application (usually as PKCS12 files with the .p12 or .pfx extension).

Client certificate authentication (if ever applied) is carried out as part of the SSL or TLS handshake, an important process that takes place before the actual data is transmitted in a SSL or TLS session.

## SSL/TLS Handshake
![alt text](./assets/ssl_tls_handshake.png)

First, the client performs a "client hello", wherein it introduces itself to the server and provides a set of security-related information. (Algorithms/Ciphers it can use).

The server responds with its own "server hello", which is accompanied with its server certificate and relevant security details based on the information initially sent by the client.

This is the optional step that initiates client certificate authentication. This will only be carried out if the server is configured to request a digital certificate from the client for the purpose of authentication.
Before this step is performed, the client inspects the server certificate for authenticity. If all goes well, it transmits additional security details and its own client certificate.

Only after both server and client have successfully authenticated each other (in addition to other security-related exchanges) will the transmission of data begin.

We know from An Overview of How Digital Certificates Work how the client is able to validate the server certificate and authenticate the server. So the remaining million-dollar question is - how does the server authenticate the client?

Just like in server certificate authentication, client certificate authentication makes use of digital signatures. For a client certificate to pass a server's validation process, the digital signature found on it should have been signed by a CA recognized by the server. Otherwise, the validation would fail.

