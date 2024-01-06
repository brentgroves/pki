# Cipher Suite

## references

<https://www.keyfactor.com/blog/cipher-suites-explained/>

## What is a cipher suite?

Cipher suites are sets of instructions that enable secure network connections through Transport Layer Security (TLS), often still referred to as Secure Sockets Layer (SSL). Behind the scenes, these cipher suites provide a set of algorithms and protocols required to secure communications between clients and servers.

To initiate an HTTPS connection, the two parties – the web server and the client – perform an SSL handshake. The handshake process is a fairly complicated one, during which the two parties agree on a mutual cipher suite. The cipher suite is then used to negotiate a secure HTTPS connection.

## Why are cipher suites required?

As we said before, the SSL handshake is a complicated process, because it leverages a variety of cryptographic functions to achieve the HTTPS connection. During the handshake, the client and the web server will use:

- A key exchange algorithm, to determine how symmetric keys will be exchanged
- An authentication or digital signature algorithm, which dictates how server authentication and client authentication (if required) will be implemented
- A bulk encryption cipher, which is used to encrypt the data
A hash/MAC function, which determines how data integrity checks will be carried out

These ciphers are required at various points of the connection to perform authentication, key generation and exchange, and a checksum to ensure integrity. To determine what specific algorithms to use, the client and the web server start by mutually deciding on the cipher suite to be used.

Cipher suites are required because of the variety of servers, operating systems and browsers. There needs to be a way to accommodate all these combinations, so cipher suites come in handy to ensure compatibility.

## How does it work?

During the handshake of a connection, when the client and server exchange information, the web server and browser compare their prioritized lists of supported cipher suites, see if they are compatible, and determine which cipher suite to use.

The decision on which cipher suite will be used depends on the web server. The agreed cipher suite is a combination of:

- Key exchange algorithms, such as RSA, DH, ECDH, DHE, ECDHE, or PSK
- Authentication/Digital Signature Algorithm, like RSA, ECDSA, or DSA
- Bulk encryption algorithms, like AES, CHACHA20, Camellia, or ARIA
- Message Authentication Code algorithms, such as SHA-256, and POLY1305

Going back to our cipher suite paradigm, let’s see what information a cipher suite provides.

![](https://www.keyfactor.com/wp-content/uploads/CipherSuite1.png)

Starting from left to right, ECDHE determines that during the handshake the keys will be exchanged via ephemeral Elliptic Curve Diffie Hellman (ECDHE). ECDSA or Elliptic Curve Digital Signature Algorithm is the authentication algorithm. AES128-GCM is the bulk encryption algorithm: AES running Galois Counter Mode with 128-bit key size. Finally, SHA-256 is the hashing algorithm.

## Why are cipher suites important?

Cipher suites are important for ensuring the security, compatibility, and performance of HTTPS connections. Just like recipes describe the required ingredients to make the perfect recipe, cipher suites dictate which algorithms to use to make a secure and reliable connection.

As we have mentioned before, it is the web server that finally determines which cipher suite will be used. Therefore, the prioritized list of cipher suites on the web server is very important. Choosing the correct ciphers to be listed on any web server is a vital exercise for any administrator and it is largely determined by the type of users connecting to the server and the technology they are using.

Users are also responsible for ensuring secure connections. Since browser vendors update their list of supported cipher suites after a vulnerability is discovered, users must install the latest browser patches to reduce the likelihood of encountering compatibility issues when weak cipher suites are deprecated on the server side.

## Supported cipher suites in TLS 1.2

Before discussing how many different cipher suites exist, let us remember that all TLS protocols prior to TLS 1.2 (i.e. TLS 1.0 and TLS 1.1) have been deprecated for various security reasons. Currently, the only acceptable TLS protocols are TLS 1.2 and TLS 1.3.

Starting with TLS 1.2, the protocol supports 37 different cipher suites. And if this number seems big, just imagine that TLS 1.2 has been around for almost a decade, during which many different systems surfaced. Add in that every cipher suite consists of four different algorithms and you end up with up to 40 different combinations of ciphers.

Of all the supported cipher suites in TLS 1.2, it is advised that we use the ones with ephemeral Diffie-Hellman algorithm. So, the advisable cipher suites are down to the following:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
...

## Identifying weak ciphers

With the introduction of TLS 1.3, many things changed to improve the security of the protocol. To start with, old, insecure ciphers have been deprecated, including:

- RC4
- DSA
- MD5
- SHA-1
- Weak Elliptic Curves
- RSA Key Exchange
- Static Diffie-Hellman (DH, ECDH)
- Block ciphers (CBC)
- Non-AEAD ciphers

## Support cipher suites in TLS 1.3

In addition, TLS 1.3 cipher suites are now much shorter than the respective TLS 1.2 suites. The cipher suites do not list the type of certificate – either RSA or ECDSA – and the key exchange mechanism – DHE or ECDHE. Therefore, the number of negotiations required to determine the encryption parameters has been reduced from four to two. Cipher suites in TLS 1.3 look like this:

![](https://www.keyfactor.com/wp-content/uploads/CipherSuite2.png)

The client initiates the handshake knowing that Ephemeral Diffie-Hellman algorithm will be used for the key exchange process, and it can send its portion of the key share during the Client Hello message. The benefit of it is that the TLS 1.3 handshake is shortened down to a single roundtrip, where the server responds with all the required information for the two parties to derive the session key and begin communicating securely.

The supported cipher suites in TLS 1.3 have now dropped to just five and are the following:

- TLS_AES_256_GCM_SHA384
- TLS_CHACHA20_POLY1305_SHA256
- TLS_AES_128_GCM_SHA256
- TLS_AES_128_CCM_8_SHA256
- TLS_AES_128_CCM_SHA256
