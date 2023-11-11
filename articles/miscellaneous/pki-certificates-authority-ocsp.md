https://www.golinuxcloud.com/tutorial-pki-certificates-authority-ocsp/
https://www.keyfactor.com/education-center/what-is-pki/
Public Key Infrastructure (PKI) provides the means to establish trust by binding public keys and identities, thus giving reasonable assurance that we’re communicating securely with who we think we are. PKI is important to using public key cryptography effectively, and is essential to understanding and using the SSL protocol.

Using public key cryptography, we can be sure that only the encrypted data can be decrypted with the corresponding private key. If we combine this with the use of a message digest algorithm to compute a signature, we can be sure that the encrypted data has not been tampered with. What’s missing is some means of ensuring that the party we’re communicating with is actually who they say they are. In other words, trust has not been established. This is where PKI fits in.

 

Certificates
At the heart of PKI is something called a certificate .
In simple terms, a certificate binds a public key with a distinguished name .
A distinguished name is simply the name of the person or entity that owns the public key to which it’s bound.
A certificate is signed with the issuer’s private key, and it contains almost all of the information necessary to verify its validity. It contains information about the subject, the issuer, and the period for which it is valid. The key component that is missing is the issuer’s certificate. The issuer’s certificate is the key component for verifying the validity of a certificate because it contains the issuer’s public key, which is necessary for verifying the signature on the subject’s certificate.
A certificate is signed with the issuer’s private key, and it contains almost all of the information necessary to verify its validity
Certificates are also created with a serial number embedded in them. The serial number is unique only to the issuer of the certificate. No two certificates issued by the same issuer should ever be assigned the same serial number. The certificate’s serial number is often used to identify a certificate quickly

Certificate Types
Some of the most used certification types are:

Wildcard: Certificates are assigned a common name when created, and the common name matches the URL of the site or entity using the certificate.
SAN:   A subject alternative name (SAN) certificate is a certificate that can have multiple common names associated with the certificate.
Code signing:   A code-signing certificate would be used to digitally sign application code that you create.
Self-signed:   A self-signed certificate is used by the root CA. Each entity within the PKI needs a certificate.
Root:   When a PKI is created, the first certificate authority installed is known as the root CA.

Certification Authorities
A Certification Authority (CA) is an organization or company that issues certificates
The CA must ensure beyond all reasonable doubt that every certificate it issues contains a public key that was issued by the party that claims to have issued it.

There are two basic types of CAs.

A private CA has the responsibility of issuing certificates only for members of its own organization, and is likewise trusted only by members of its own organization.
A public CA, such as VeriSign or Thawte, has the responsibility of issuing certificates for any member of the public, and must be trusted by the public

Certificate Hierarchies
A certificate that is issued by a CA can be used to issue and sign another certificate, if the issued certificate is created with the appropriate permissions to do so. In this way, certificates can be chained.
At the root of the chain is the root CA’s certificate. Because it is at the root of the chain and there is no other authority to sign its certificate, the root CA signs its own certificate. Such a certificate is known as a self-signed certificate.

Certificate Extensions
The most widely accepted format for certificates is the X.509 format. There are three versions of the format, known as X.509v1, X.509v2, and X.509v3. The most recent revision of the standard was introduced in 1996, and most, if not all, modern software now supports it. A large number of changes were made between X.509v1 and X.509v3, but perhaps one of the most significant features introduced in the X.509v3 standard is its support of extensions.

 Certificate Formats
There are different certificate formats because of the way the information is stored in the certificate. The following identifies common file formats for certificates:

DER/CER   Distinguished Encoding Rules (DER) and Canonical Encoding Rules (CER) are binary file formats used to store information in the certificate file. DER-formatted files can have a .der or a .cer file extension.
PEM   Privacy-enhanced Electronic Mail (PEM) is an ASCII file format that can have a file extension of .pem, .crt, .cer, or .key. PEM files are very common and start with -----BEGIN CERTIFICATE----- and end with -----END CERTIFICATE-----.
PFX/P12   The Personal Information Exchange (PFX) format, also known as the P12 or PKCS#12 format, is a binary file format that is common with Microsoft environments for importing and exporting certificates. PFX formatted files have an extension of .pfx or .p12.
P7B   The P7B format, also known as PKCS#7, is another ASCII file format used to store certificate information. If you open the ASCII file, you will see that it begins with the text -----BEGIN PKCS7----- and ends with -----END PKCS7-----. P7B files can have an extension of .p7b or .p7c.

Certificate Revocation Lists
Once a certificate has been issued, it is generally put into production, where it will be distributed to many clients. If an attacker compromises the associated private key, he now has the ability to use the certificate even though it doesn’t belong to him. Assuming the proper owner is aware of the compromise, a new certificate with a new key pair should be obtained and put into use. In this situation there are two certificates for the same entity—both are technically valid, but one should not be trusted. The compromised certificate will eventually expire, but in the meantime, how will the world at large know not to trust it?

The answer lies in something called a certificate revocation list (CRL). A CRL contains a list of all of the revoked certificates a CA has issued that have yet to expire. When a certificate is revoked, the CA declares that the certificate should no longer be trusted.

 Online Certificate Status Protocol
The Online Certificate Status Protocol (OCSP), formally specified in RFC 2560, is a relatively new addition to PKI. Its primary aim is to address some of the distribution problems that have traditionally plagued CRLs.

Using OCSP, an application makes a connection to an OCSP responder and requests the status of a certificate by passing the certificate’s serial number. The responder replies “good,” “revoked,” or “unknown.” A “good” response indicates that the certificate is valid, so far as the responder knows. This does not necessarily mean that the certificate was ever issued, just that is hasn’t been revoked. A “revoked” response indicates that the certificate has been issued and that it has indeed been revoked. An “unknown” response indicates that the responder doesn’t know anything about the certificate. A typical reason for this response could be that the certificate was issued by a CA that is unknown to the responder.

How SSL/TLS Works?
When you SSL-enable a web site, you should understand how the encryption works with the web site. The following are the general steps taken by a client visiting your web site and the web server:

The client sends a request for a web page to the secure web site using https:// in the URL. This makes a connection to port 443 by default.
The server sends the public key to the client.
The client validates the certificate and ensures it has not expired or been revoked.
The client creates a random symmetric key (known as a session key) used to encrypt the web page content, and then encrypts the symmetric key with the public key obtained from the web server.
The encrypted information is sent to the web server. The web server decrypts and obtains the symmetric key (session key). The web server uses the symmetric key to encrypt information between the client and the server.

----------
Anyone can use the public portion of a certificate to verify that it was actually issued by the CA by confirming who owns the private key used to sign the certificate. And, assuming they deem that CA trustworthy, they can verify that anything they send to the certificate holder will actually go to the intended recipient and that anything signed using that certificate holder’s private key was indeed signed by that person/device. 

One important part of this process to note is that the CA itself has its own private key and corresponding public key, which creates the need for CA hierarchies. 

How CA Hierarchies and Root CAs Create Layers of Trust
Since each CA has a certificate of its own, layers of trust get created through CA hierarchies — in which CAs issue certificates for other CAs. However, this process is not circular, as there is ultimately a root certificate. Normally, certificates have an issuer and a subject as two separate parties, but these are the same parties for root CAs, meaning that root certificates are self-signed. As a result, people must inherently trust the root certificate authority to trust any certificates that trace back to it. 

Root CA Security is of Utmost Importance
All of this makes the security of private keys extra important for CAs. A private key falling into the wrong hands is bad in any case, but it’s particularly devastating for CAs, because then someone can issue certificates fraudulently.  

Security controls and the impact of loss become even more severe as you move up the chain in a CA hierarchy because there is no way to revoke a root certificate. Should a root CA become compromised, the organization needs to make that security breach public. As a result, root CAs have the most stringent security measures. 

To meet the highest security standards, root CAs should almost never be online. As a best practice, root CAs should store their private keys in NSA-grade safes within state of the art data centers with 24/7 security via cameras and physical guards. All of these measures might seem extreme, but they’re necessary to protect the authenticity of a root certificate. 

Although a root CA should be offline 99.9% of the time, there are certain instances where it does need to come online. Specifically, root CAs need to come online for the creation of public keys, private keys and new certificates as well as to ensure that its own key material is still legitimate and hasn’t been damaged or compromised in any way. Ideally, root CAs should run these tests about 2-4 times a year. 

Finally, it’s important to note that root certificates do expire. Root certificates typically last for 15-20 years (compared to approximately seven years for certificates from subordinate CAs). Introducing and building trust in a new root isn’t easy, but it’s important that these certificates do expire because the longer they run, the more vulnerable they become to security risks. 

Determining the Optimal Level of Tiers in Your PKI’s CA Hierarchy
A CA hierarchy typically involves two tiers, following the chain of Root Certificate Authority → Subordinate Certificate Authorities → End-Entity Certificates. 

