Using Signed Certificates
Last Updated 4 years ago

Subject: How to Use Certificates
Manufacturer: Tridium
Model Range: AX/N4
Software Version: Tridium N4/AX


Introduction
Establishing a fully managed certificate environment is known as a PKI (Public key infrastructure) environment, and a proper implementation would require the customer have either their own internal Certificate Authority server or use an external public CA such as Verisign/GoDaddy/Comodo etc...

The core process remains the same. All devices in the environment that require a certificate for secure communications would submit a CSR (Certificate Signing Request), which is given to the CA server and signed. The signed certificate is then installed into the device (Niagara JACE or Supervisor for example) and used for the FOXS station comms, Platform form comms and the web server.

In order for connecting clients to experience a seamless secure connection without errors or bad certificate warnings, they need to trust the presented certificate. This is done by ensuring all the client computers etc have a copy of the full chain of trust installed, this usually consists of the CA root public cert and any intermediates. If this is in place, then the secure connection and trust will be seamless.

2.Step by Step Guide
The Niagara certificate manager is very particular with how the full chain of trust is imported. What must happen is as follows:

1. Generate a certificate in Niagara certificate manager

2. Export the private key and keep it safe. This is a .PEM file.

3. Create a signing request based on the newly created certificate.

4. Give this CSR to your CA of choice. I have tested with GoDaddy and Comodo, but there are many more. These two works natively in PEM format so you don't have to mess around converting certificate formats.

5. Download the signed cert and all intermediates + CA root certs from the CA. (With GoDaddy, they bundle the intermediates and root into a single PEM formatted .CER file for you).

6. Combine the signed certificate, intermediates and root into a single PEM formatted .PEM file. I also include private key in the top of the chain file, although this isn’t required since the original signing request has been generated from Niagara and therefore Niagara already has the private key. An example chain file is attached for reference; however, I have purposefully edited the key & cert, so they cannot be used. The process to make a chain file is as follows:



a. Open a new text file, copy the private key from the original Niagara export PEM file into the top

b. Copy the signed certificate you got from the CA into the file below the private key.

c. Copy any intermediate certificates from the CA below the signed cert that you just copied in.

d. Copy the root ca certificate at the bottom of the file. Like I say, in the case of GoDaddy, root and intermediates are bundled for you, so copy all from that file into the new file below the signed cert you copied in.

e. Save this file with the. PEM extension. For example, MYCERTIFICATENAME.PEM


7. Open the certificate manager on the JACE and go to System Trust Store. Import the chain file and you should be shown a list of certificates to import. Select only the intermediates and the root. Import them.

8. Go to the User Key Store and click import. Choose the chain file. This should show you the signed certificate with the correct domain name. Import this. You should see the green tick against your certificate to show its now trusted (it may have already gone green).

9. Go to web service / fox service / platform admin and set them all to use the new certificate.

3. Solution For connecting clients to experience a seamless secure connection without errors or bad certificate warnings, they need to trust the presented certificate. This is done by ensuring all the client computers etc have a copy of the full chain of trust installed, this usually consists of the CA root public cert and any intermediates. If this is in place, then the secure connection and trust will be seamless.

It is worth noting that if a public CA has been used to sign certificates that their root chain of trust is usually included in the trust store of most modern operating systems by default.

This whole process is very specific and is always owned and managed by the site IT admin team. We cannot assist with implementing such a solution due to the specific choices that must be made by the customer due to the design of their IT environment.
