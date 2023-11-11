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

Managing Revocation Through Certificate Revocation Lists
If a subordinate CA gets compromised in any way or wants to revoke a certificate for any reason, it must publish a revocation list of any issued certificates that should not be trusted. This list is known as a Certificate Revocation List (CRL) and is critical to PKI design. 

While CAs must issue CRLs, it’s up to the discretion of certificate consumers if they check these lists and how they respond if a certificate has been revoked. Once again, this is a prime example of how digital certificates are similar to driver’s licenses since the vetting process typically depends on the need for the certificate (think about the difference between using a recently expired license to buy alcohol vs. to pass a TSA checkpoint). 

In many cases, certificate consumers choose not to check CRLs because doing so slows down the authentication process. Certificate consumers can also choose how far back to go within the CA hierarchy as part of the check, keeping in mind that the further back they go, the longer the process takes. 

Although checking CRLs — and going all the way to the root CA to do so — slows down the authentication process, doing so is becoming more standard as more things go online and rely on digital certificates for security. Consider the case of web browsers. Many web browsers previously didn’t check certificates because it slowed down the browsing experience, but now these checks are commonplace as internet security becomes more important. 

Critically, the CRLs themselves have an expiration date, and if a CRL expires, every certificate issued by the CA becomes invalid. While CAs primarily focus on making sure certificates don’t expire — which is important — it’s also important they make sure CRLs don’t expire because if that happens it can take down the entire PKI. When root CAs do go online, they also check to make sure that CRLs from subordinate CAs have not expired for this reason. 

Trusted Root Certificates
Today, every device and system that goes online  (e.g. phones, laptops, servers, operating systems) needs to interact with certificates. This widespread interaction with certificates has led to the concept of a trusted root certificate within devices and operating systems. 

For example, all Microsoft computers have a trusted root store. Any certificate that can be traced back to that trusted root store will be automatically trusted by the computer. Each device and operating system comes with a pre-set trusted root store, but machine owners can set rules to trust additional certificates or to not trust certificates that were pre-set as trusted. 

The Third Wave: New Uses and Growing Pains (2011-Today)
The third wave of PKI, which we’re still experiencing today, includes several new uses around the Internet of Things (IoT) and some growing pains with scaling PKI along the way. 

Today, organizations issue millions of certificates to authenticate a fully mobile, multi-device workforce. Beyond employee devices, organizations also have to manage embedded certificates in all sorts of cloud systems. Finally, the rise of the IoT has led to millions of new connected devices, each of which needs to be secured, authenticated, and able to get firmware updates. All of these connections make PKI more important than ever and have led to enormous growth in this space. 

But as PKI becomes more important and more prevalent, it also gets more challenging. Specifically, today’s connected digital world creates PKI management challenges around getting certificates where they need to go, ensuring certificates are properly vetted and mapped, and monitoring already-issued certificates.  

Overseeing, managing, and updating millions of certificates is such a big job that most organizations now rely on third-party managed service providers and specialized certificate management tools to handle their PKI. This trend is similar to the move to the cloud, as organizations shifted from owned data servers to third-party cloud computing providers. 

Engaging a managed service provider for PKI allows each organization to focus their employee’s expertise on areas directly related to their line of business (rather than operating infrastructure) and protects against turnover among PKI experts. Most importantly, it improves PKI management and security by providing access to a large team that specializes in developing and running best practice PKI programs. 

What are Common Challenges that PKI Solves?
A wide variety of use cases exist for PKI. Some of the most common PKI use cases include: 

SSL/TLS certificates to secure web browsing experiences and communications 
Digital signatures on software 
Restricted access to enterprise intranets and VPNs 
Password-free Wifi access based on device ownership 
Email and data encryption
 

One of the most explosive uses for PKI that is just now taking off centers around authenticating and securing a wide variety of IoT devices. These use cases span across industries, as any connected device — no matter how innocuous it may seem — requires security in this day and age. For instance, The Home Depot data breach first started because hackers were able to access the retailer’s point of sale system by getting onto the network posing as an unauthenticated HVAC unit. 

Some of the most compelling PKI use cases today center around the IoT. Auto manufacturers and medical device manufacturers are two prime examples of industries currently introducing PKI for IoT devices. 

