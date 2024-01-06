# Cipher

## references

<https://www.techtarget.com/searchsecurity/definition/cipher>

## What is a cipher?

In cryptology, the discipline concerned with the study of cryptographic algorithms, a cipher is an algorithm for encrypting and decrypting data.

Symmetric key encryption, also called secret key encryption, depends on the use of ciphers, which operate symmetrically. With symmetric encryption algorithms, the same encryption key is applied to data in the same way, whether the objective is to convert plaintext to ciphertext or ciphertext to plaintext. A cipher transforms data by processing the original, plaintext characters or other data into ciphertext. The ciphertext should appear as random data.

Traditionally, ciphers used these two main types of transformation:

1. Transposition ciphers keep all the original bits of data in a byte but mix their order.
2. Substitution ciphers replace specific data sequences with other data sequences. For example, one type of substitution would be to transform all bits with a value of 1 to a value of 0, and vice versa.

The data output from either method is called the ciphertext.

Modern ciphers enable private communication in many different networking protocols, including the Transport Layer Security (TLS) protocol and others that offer encryption of network traffic. Many communication technologies, including phones, digital television and ATMs, rely on ciphers to maintain security and privacy.

## How do ciphers work?

A cipher uses a system of fixed rules -- an encryption algorithm -- to transform plaintext, a legible message, into ciphertext, an apparently random string of characters. Ciphers can be designed to encrypt or decrypt bits in a stream, known as stream ciphers. Or they can process ciphertext in uniform blocks of a specified number of bits, known as block ciphers

![](https://cdn.ttgtmedia.com/rms/onlineimages/encryption_operation-f.png)

Modern cipher implementations depend on the algorithm and a secret key, which is used by the encryption algorithm to modify data as it is encrypted. Ciphers that use longer keys, measured in bits, are more effective against **[brute-force](https://www.techtarget.com/searchsecurity/definition/brute-force-cracking)** attacks. The longer the key length, the more brute-force attempts are necessary to expose the plaintext. While cipher strength is not always dependent on the length of the key, experts recommend modern ciphers be configured to use keys of at least 128 bits or more, depending on the algorithm and the use case.
