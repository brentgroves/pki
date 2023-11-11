https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake/

What happens in a TLS handshake? | SSL handshake
In a TLS/SSL handshake, clients and servers exchange SSL certificates, cipher suite requirements, and randomly generated data for creating session keys.
TLS is an encryption and authentication protocol designed to secure Internet communications. A TLS handshake is the process that kicks off a communication session that uses TLS. During a TLS handshake, the two communicating sides exchange messages to acknowledge each other, verify each other, establish the cryptographic algorithms they will use, and agree on session keys. TLS handshakes are a foundational part of how HTTPS works.

What happens during a TLS handshake?
During the course of a TLS handshake, the client and server together will do the following:

Specify which version of TLS (TLS 1.0, 1.2, 1.3, etc.) they will use
Decide on which cipher suites (see below) they will use
Authenticate the identity of the server via the server’s public key and the SSL certificate authority’s digital signature
Generate session keys in order to use symmetric encryption after the handshake is complete
