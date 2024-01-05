# Generate and Install Certificates

## References

<https://www.golinuxcloud.com/openssl-create-certificate-chain-linux/>

- First generate private key ca.key, we will use this private key to create Certificate Authority certificate

## Create the private key for the root certificate

- This is done only once please don't overwrite this private key.

```bash
openssl genrsa -out ~/src/repsys/volumes/pki/rootCA/private/ca.key.pem 4096
chmod 400 ~/src/repsys/volumes/pki/rootCA/private/ca.key.pem

# We will use openssl command to view the content of private key:
openssl rsa -noout -text -in ~/src/repsys/volumes/pki/rootCA/private/ca.key.pem
```

## Create the root CA certificate

OpenSSL create certificate chain requires Root and Intermediate Certificate. In this step you'll take the place of VeriSign, Thawte, etc.
Use the Root CA key cakey.pem to create a Root CA certificate cacert.pem
Give the root certificate a long expiry date. Once the root certificate expires, all certificates signed by the CA become invalid.
Whenever you use the openssl req tool, you must specify a configuration file to use with the -config option, otherwise OpenSSL will default to /etc/pki/tls/openssl.cnf
We will use v3_ca extensions to create CA certificate
note: The Common Name (CN) of the CA and the Server certificates must NOT match or else a naming collision will occur and you'll get errors later on.

Use below command to create Root Certificate Authority Certificate cacert.pem. I have specified the Subj inline to the same command, you can update the command based on your environment.

- Please don't create this root certificate again since it is good for 20 years.

```bash

# This certificate is good for 365*10*2 days = 7300 days = 20 years
openssl req -config openssl_root.cnf -key ~/src/repsys/volumes/pki/rootCA/private/ca.key.pem -new -x509 -days 7300 -sha256 -extensions v3_ca -out ~/src/repsys/volumes/pki/rootCA/certs/ca.cert.pem -subj "/C=US/ST=Indiana/L=Albion/O=Mobex Global/OU=Information Systems/CN=Root CA"
pretty form:
openssl req \
-config ~/src/repsys/volumes/pki/openssl_root.cnf \
-key ~/src/repsys/volumes/pki/rootCA/private/ca.key.pem \
-new -x509 -days 7300 -sha256 -extensions v3_ca \
-out ~/src/repsys/volumes/pki/rootCA/certs/ca.cert.pem \
-subj "/C=US/ST=Indiana/L=Albion/O=Mobex Global/CN=Root CA"

# The CA certificate can be world readable so that it can be used to sign the cert by anyone
chmod 444 ~/src/repsys/volumes/pki/rootCA/certs/ca.cert.pem
ls -al ~/src/repsys/volumes/pki/rootCA/certs/ca.cert.pem

# Execute the below command for openssl verify root CA certificate
openssl x509 -noout -text -in ~/src/repsys/volumes/pki/rootCA/certs/ca.cert.pem
# The output shows:
# the Signature Algorithm used
# the dates of certificate Validity
# the Public-Key bit length
# the Issuer, which is the entity that signed the certificate
# the Subject, which refers to the certificate itself

# note: The Issuer and Subject are identical as the certificate is self-signed.

# verify root ca

https://crt.sh/lintcert
https://www.sslshopper.com/certificate-decoder.html

# Generate the intermediate CA key pair and certificate

# Create an RSA key pair for the intermediate CA without a password and secure the file by removing permissions to groups and others.
openssl genrsa -out ~/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem 4096
chmod 400 ~/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem
ls -al ~/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem

# verify key
openssl rsa -noout -text -in ~/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem

# Create the intermediate CA certificate signing request (CSR)
openssl req -config openssl_intermediate.cnf -key ~/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem -new -sha256 -out ~/src/repsys/volumes/pki/intermediateCA/csr/intermediate.csr.pem -subj "/C=US/ST=Indiana/L=Albion/O=Mobex Global/OU=Information Systems/CN=Intermediate CA"

openssl req \
-config ~/src/repsys/volumes/pki/openssl_intermediate.cnf \
-key ~/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem \
-new -sha256 -out ~/src/repsys/volumes/pki/intermediateCA/csr/intermediate.csr.pem -subj "/C=US/ST=Indiana/L=Albion/O=Mobex Global/CN=Intermediate CA"

# note differences between this and ca root
# different config file
# Intermidiate CSR: -new -sha256
# Root CSR: -new -x509 -days 7300 
# Also note the generation of the cert does not happen until the next step unlike for the root cert.

openssl req \
-config ~/src/repsys/volumes/pki/openssl_root.cnf \
-key ~/src/repsys/volumes/pki/rootCA/private/ca.key.pem \
-new -x509 -days 7300 -sha256 -extensions v3_ca \
-out ~/src/repsys/volumes/pki/rootCA/certs/ca.cert.pem \
-subj "/C=US/ST=Indiana/L=Albion/O=Mobex Global/CN=Root CA"


# note removed the OrganizationUnitName from the subject.
# ERROR OrganizationalUnitName is prohibited if...the certificate was issued on or after September 1, 2022

# Sign the intermediate CSR with the root CA key
# https://www.openssl.org/docs/man1.1.1/man1/ca.html
openssl ca \
-rand_serial -config ~/src/repsys/volumes/pki/openssl_root.cnf \
-extensions v3_intermediate_ca \
-days 3650 -notext -md sha256 \
-in ~/src/repsys/volumes/pki/intermediateCA/csr/intermediate.csr.pem -out ~/src/repsys/volumes/pki/intermediateCA/certs/intermediate.cert.pem

# https://www.openssl.org/docs/man1.1.1/man1/ca.html
# The ca command is a minimal CA application. It can be used to sign certificate requests in a variety of forms and generate CRLs it also maintains a text database of issued certificates and their status.
# The intermediate cert is the only one that added an entry to the index.txt and serial database because it used the ca command

# Assign 444 permission to the CRT to make it readable by everyone
chmod 444 ~/src/repsys/volumes/pki/intermediateCA/certs/intermediate.cert.pem
ls -al ~/src/repsys/volumes/pki/intermediateCA/certs/intermediate.cert.pem

# Verify the Intermediate CA Certificate content
openssl x509 -noout -text -in ~/src/repsys/volumes/pki/intermediateCA/certs/intermediate.cert.pem
# https://crt.sh/lintcert
# https://www.sslshopper.com/certificate-decoder.html

# Next verify the intermediate certificate against the root certificate. An OK indicates that the chain of trust is intact.
pushd ~/src/repsys/volumes/pki/intermediateCA
openssl verify -CAfile ~/src/repsys/volumes/pki/rootCA/certs/ca.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/intermediate.cert.pem
/home/brent/src/repsys/volumes/pki/intermediateCA/certs/intermediate.cert.pem: OK

# Generate OpenSSL Create Certificate Chain (Certificate Bundle)

# To create certificate chain (certificate bundle), concatenate the intermediate and root certificates.
# In the below example I have combined our Root and Intermediate CA certificates to create our certificate chain. We will use this file later to verify certificates signed by the intermediate CA.

cat ~/src/repsys/volumes/pki/intermediateCA/certs/intermediate.cert.pem ~/src/repsys/volumes/pki/rootCA/certs/ca.cert.pem > ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem

# verify certificate signature using the below command:
pushd ~/src/repsys/volumes/pki/intermediateCA
openssl verify -CAfile ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/intermediate.cert.pem
/home/brent/src/repsys/volumes/pki/intermediateCA/certs/intermediate.cert.pem: OK

# https://www.golinuxcloud.com/openssl-generate-csr-create-san-certificate/
# Create a subject alternative name, SAN, Certificate using openssl.
# Generate server private key or use the one generated from Mach2
# Sam used niagara to generate a cert and exported the private key. This cert had a san field of frt-kors43 but no policy field. I did not use Niagara's cert but did use it's private key to generate a CSR and SAN certificate. Using this method niagara successfully imported the key,cert,cert chain pem files generated and that included the private key Sam sent me.

# The following private keys are located in the /home/brent/src/repsys/volumes/pki/intermediateCA directory:
# hrt-kors43.busche-cnc.com.key.pem
# Sam sent this file which included both a certificate and private key. I didn't need the certificate so I removed it from the file.
# mgedo-srv-mach2.key.pem
# Sam exported this private key after generating a certificate from Niagara for the Edon Mach2 server.
# sams.key.pem
# Sam exported this private key after generating a certificate from Niagara for frt-kors43.

# We generated the following private keys for various report system K8s cluster. 
# reports1 kong ingress controller
openssl genpkey -algorithm RSA \
-out ~/src/repsys/volumes/pki/intermediateCA/private/reports1.busche-cnc.com.san.key.pem
# reports51
openssl genpkey -algorithm RSA \
-out ~/src/repsys/volumes/pki/intermediateCA/private/reports51.busche-cnc.com.san.key.pem
# reports11
openssl genpkey -algorithm RSA \
-out ~/src/repsys/volumes/pki/intermediateCA/private/reports11.busche-cnc.com.san.key.pem
## reports-alb
openssl genpkey -algorithm RSA \
-out ~/src/repsys/volumes/pki/intermediateCA/private/reports-alb.busche-cnc.com.san.key.pem
# moto and home
openssl genpkey -algorithm RSA \
-out ~/src/repsys/volumes/pki/intermediateCA/private/moto.busche-cnc.com.san.key.pem
# devcon2
openssl genpkey -algorithm RSA \
-out ~/src/repsys/volumes/pki/intermediateCA/private/devcon2.busche-cnc.com.san.key.pem
# frt-kors43
# This was from the failed Mach2 Attempt1 when Sam generated the CSR and I signed it. You must generate a certificate from Niagara and use the private key to generate a SAN certificate or Niagara won't recognize the certificate.
openssl genpkey -algorithm RSA \
-out ~/src/repsys/volumes/pki/intermediateCA/private/frt-kors43.busche-cnc.com.san.key.pem

# Generate CSR 
# Now in order to create a SAN certificate we must generate a new CSR i.e. Certificate Signing Request which we will use in next step with openssl to generate a SAN certificate. Accept the default Country Name when prompted no other subject fields should be added.
pushd ~/src/repsys/volumes/pki/intermediateCA

# hrt-kors43.busche-cnc.com.key
# I can ping this server. ping hrt-kors43.busche-cnc.com
openssl req \
-config ~/src/repsys/volumes/pki/openssl_intermediate_for_san_certificate.cnf \
-key ~/src/repsys/volumes/pki/intermediateCA/private/hrt-kors43.busche-cnc.com.key.pem \
-new -sha256 \
-out ~/src/repsys/volumes/pki/intermediateCA/csr/hrt-kors43.busche-cnc.com.san.csr.pem

# mgedo-srv-mach2.busche-cnc.com
# note: can not ping this but can access it with chrome
openssl req \
-config ~/src/repsys/volumes/pki/openssl_intermediate_for_san_certificate.cnf \
-key ~/src/repsys/volumes/pki/intermediateCA/private/mgedo-srv-mach2.key.pem \
-new -sha256 \
-out ~/src/repsys/volumes/pki/intermediateCA/csr/mgedo-srv-mach2.san.csr.pem

# frt-kors43.busche-cnc.com
openssl req \
-config ~/src/repsys/volumes/pki/openssl_intermediate_for_san_certificate.cnf \
-key ~/src/repsys/volumes/pki/intermediateCA/private/sams.key.pem \
-new -sha256 \
-out ~/src/repsys/volumes/pki/intermediateCA/csr/sams.san.csr.pem

# reports-alb
openssl req \
-config ~/src/repsys/volumes/pki/openssl_intermediate_for_san_certificate.cnf \
-key ~/src/repsys/volumes/pki/intermediateCA/private/reports-alb.busche-cnc.com.san.key.pem \
-new -sha256 \
-out ~/src/repsys/volumes/pki/intermediateCA/csr/reports-alb.busche-cnc.com.san.csr.pem

# reports1 for kong ingress control
openssl req \
-config ~/src/repsys/volumes/pki/openssl_intermediate_for_san_certificate.cnf \
-key ~/src/repsys/volumes/pki/intermediateCA/private/reports1.busche-cnc.com.san.key.pem \
-new -sha256 \
-out ~/src/repsys/volumes/pki/intermediateCA/csr/reports1.busche-cnc.com.san.csr.pem

# reports11
openssl req \
-config ~/src/repsys/volumes/pki/openssl_intermediate_for_san_certificate.cnf \
-key ~/src/repsys/volumes/pki/intermediateCA/private/reports11.busche-cnc.com.san.key.pem \
-new -sha256 \
-out ~/src/repsys/volumes/pki/intermediateCA/csr/reports11.busche-cnc.com.san.csr.pem

# reports51
openssl req \
-config ~/src/repsys/volumes/pki/openssl_intermediate_for_san_certificate.cnf \
-key ~/src/repsys/volumes/pki/intermediateCA/private/reports51.busche-cnc.com.san.key.pem \
-new -sha256 \
-out ~/src/repsys/volumes/pki/intermediateCA/csr/reports51.busche-cnc.com.san.csr.pem

# moto and home
openssl req \
-config ~/src/repsys/volumes/pki/openssl_intermediate_for_san_certificate.cnf \
-key ~/src/repsys/volumes/pki/intermediateCA/private/moto.busche-cnc.com.san.key.pem \
-new -sha256 \
-out ~/src/repsys/volumes/pki/intermediateCA/csr/moto.busche-cnc.com.san.csr.pem

# devcon2
openssl req \
-config ~/src/repsys/volumes/pki/openssl_intermediate_for_san_certificate.cnf \
-key ~/src/repsys/volumes/pki/intermediateCA/private/devcon2.busche-cnc.com.san.key.pem \
-new -sha256 \
-out ~/src/repsys/volumes/pki/intermediateCA/csr/devcon2.busche-cnc.com.san.csr.pem

# frt-kors43
openssl req \
-config ~/src/repsys/volumes/pki/openssl_intermediate_for_san_certificate.cnf \
-key ~/src/repsys/volumes/pki/intermediateCA/private/frt-kors43.busche-cnc.com.san.key.pem \
-new -sha256 \
-out ~/src/repsys/volumes/pki/intermediateCA/csr/frt-kors43.busche-cnc.com.san.csr.pem

# Sign the CSR with our certificate chain bundle and intermediate certificates private key to create a Subject Alternative Name, SAN, certificate.  Make sure to create a new configuration file such as, cert_ext_reports11.cnf, for each server. This config file contains the FQDN in which the certificate is valid for.  These certificates are only valid for 1 year which is a minimum now imposed by browsers.

# reports1 for kong ingress controller
openssl x509 -req \
-in /home/brent/src/repsys/volumes/pki/intermediateCA/csr/reports11.busche-cnc.com.san.csr.pem \
-CA /home/brent/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem \
-CAkey /home/brent/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem \
-out /home/brent/src/repsys/volumes/pki/intermediateCA/certs/reports11.busche-cnc.com.san.cert.pem \
-CAcreateserial -days 365 -sha256 \
-extfile /home/brent/src/repsys/volumes/pki/cert_ext_reports11.cnf

# reports11
openssl x509 -req \
-in /home/brent/src/repsys/volumes/pki/intermediateCA/csr/reports11.busche-cnc.com.san.csr.pem \
-CA /home/brent/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem \
-CAkey /home/brent/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem \
-out /home/brent/src/repsys/volumes/pki/intermediateCA/certs/reports11.busche-cnc.com.san.cert.pem \
-CAcreateserial -days 365 -sha256 \
-extfile /home/brent/src/repsys/volumes/pki/cert_ext_reports11.cnf

# reports51
openssl x509 -req \
-in /home/brent/src/repsys/volumes/pki/intermediateCA/csr/reports51.busche-cnc.com.san.csr.pem \
-CA /home/brent/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem \
-CAkey /home/brent/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem \
-out /home/brent/src/repsys/volumes/pki/intermediateCA/certs/reports51.busche-cnc.com.san.cert.pem \
-CAcreateserial -days 365 -sha256 \
-extfile /home/brent/src/repsys/volumes/pki/cert_ext_reports51.cnf

# reports-alb
openssl x509 -req \
-in /home/brent/src/repsys/volumes/pki/intermediateCA/csr/reports-alb.busche-cnc.com.san.csr.pem \
-CA /home/brent/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem \
-CAkey /home/brent/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem \
-out /home/brent/src/repsys/volumes/pki/intermediateCA/certs/reports-alb.busche-cnc.com.san.cert.pem \
-CAcreateserial -days 365 -sha256 \
-extfile /home/brent/src/repsys/volumes/pki/cert_ext_reports-alb.cnf

# moto and home
openssl x509 -req \
-in /home/brent/src/repsys/volumes/pki/intermediateCA/csr/moto.busche-cnc.com.san.csr.pem \
-CA /home/brent/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem \
-CAkey /home/brent/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem \
-out /home/brent/src/repsys/volumes/pki/intermediateCA/certs/moto.busche-cnc.com.san.cert.pem \
-CAcreateserial -days 365 -sha256 \
-extfile /home/brent/src/repsys/volumes/pki/cert_ext_moto.cnf

# devcon2
openssl x509 -req \
-in /home/brent/src/repsys/volumes/pki/intermediateCA/csr/devcon2.busche-cnc.com.san.csr.pem \
-CA /home/brent/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem \
-CAkey /home/brent/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem \
-out /home/brent/src/repsys/volumes/pki/intermediateCA/certs/devcon2.busche-cnc.com.san.cert.pem \
-CAcreateserial -days 365 -sha256 \
-extfile /home/brent/src/repsys/volumes/pki/cert_ext_devcon2.cnf

# frt-kors43
# Mach2 attempt2 in which we generate the certificate and private key with Niagara and we discard the certificate and generate our own using Niagara generated private key.
openssl x509 -req \
-in /home/brent/src/repsys/volumes/pki/intermediateCA/csr/sams.san.csr.pem \
-CA /home/brent/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem \
-CAkey /home/brent/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem \
-out /home/brent/src/repsys/volumes/pki/intermediateCA/certs/sams.san.cert.pem \
-CAcreateserial -days 365 -sha256 \
-extfile /home/brent/src/repsys/volumes/pki/cert_ext_frt-kors43.cnf

# mgedo-srv-mach2.busche-cnc.com
pushd ~/src/repsys/volumes/pki/intermediateCA
openssl x509 -req \
-in /home/brent/src/repsys/volumes/pki/intermediateCA/csr/mgedo-srv-mach2.san.csr.pem \
-CA /home/brent/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem \
-CAkey /home/brent/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem \
-out /home/brent/src/repsys/volumes/pki/intermediateCA/certs/mgedo-srv-mach2.san.cert.pem \
-CAcreateserial -days 365 -sha256 \
-extfile /home/brent/src/repsys/volumes/pki/cert_ext_mgedo-srv-mach2.cnf

# hrt-kors43.busche-cnc.com
pushd ~/src/repsys/volumes/pki/intermediateCA
openssl x509 -req \
-in /home/brent/src/repsys/volumes/pki/intermediateCA/csr/hrt-kors43.busche-cnc.com.san.csr.pem \
-CA /home/brent/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem \
-CAkey /home/brent/src/repsys/volumes/pki/intermediateCA/private/intermediate.key.pem \
-out /home/brent/src/repsys/volumes/pki/intermediateCA/certs/hrt-kors43.busche-cnc.com.san.cert.pem \
-CAcreateserial -days 365 -sha256 \
-extfile /home/brent/src/repsys/volumes/pki/cert_ext_hrt-kors43.cnf

Certificate request self-signature ok
subject=C = US
Validity
            Not Before: Sep 21 18:55:55 2023 GMT
            Not After : Sep 20 18:55:55 2024 GMT
Serial number: 50D4396FE4E68BFDFF25BB5849DA436BA29D4E2A

# Since we used x509 instead of ca to sign the cert the index.txt and serial database was not updated but the certs/ca-chain/ca-chain-bundle.cert.srl file serial number was updated but we probably should have a list of all of the serial numbers issued not just the latest.

# After we create a SAN certificate, next we can check the content of our server certificate to make sure openssl signs the CSR with the correct Subject Alternative Name.

openssl x509 -noout -text -in ~/src/repsys/volumes/pki/intermediateCA/certs/reports11.busche-cnc.com.san.cert.pem
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            50:d4:39:6f:e4:e6:8b:fd:ff:25:bb:58:49:da:43:6b:a2:9d:4e:22
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: C = US, ST = Indiana, O = Mobex Global, CN = Intermediate CA
        Validity
            Not Before: Aug 19 00:26:13 2023 GMT
            Not After : Aug 18 00:26:13 2024 GMT
        Subject: C = US
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                .....
       X509v3 extensions:
            X509v3 Basic Constraints:
                CA:FALSE
            X509v3 Subject Key Identifier:
                74:C8:F3:53:1E:2C:0C:C3:3D:7B:F6:15:44:A1:70:EF:E4:F6:AC:9F
            X509v3 Authority Key Identifier:
                keyid:B2:04:7B:21:5A:89:EA:FE:3F:91:F2:97:D2:46:3D:42:AA:AA:60:66

            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication
            X509v3 Subject Alternative Name: 
                DNS:moto.busche-cnc.com
            Authority Information Access: 
                OCSP - URI:http://ocsp.busche-cnc.com/
                CA Issuers - URI:http://busche-cnc.com/ca.html

            X509v3 Certificate Policies: 
                Policy: 2.23.140.1.2.1
                Policy: X509v3 Any Policy

# Verify private/public key pair match
# https://www.sslshopper.com/certificate-key-matcher.html
or
openssl pkey -in privateKey.key -pubout -outform pem | sha256sum
openssl x509 -in certificate.crt -pubkey -noout -outform pem | sha256sum
openssl req -in CSR.csr -pubkey -noout -outform pem | sha256sum

# Check with linter
# https://crt.sh/lintcert
# https://www.sslshopper.com/certificate-decoder.html
# cablint INFO TLS Server certificate identified
# x509lint INFO Checking as leaf certificate
# zlint NOTICE Check if certificate has enough embedded SCTs to meet Apple CT Policy

# verify certificate
# Next openssl verify intermediate certificate against the root certificate. An OK indicates that the chain of trust is intact.

# verifying with just the intermediate certificate fails as expected
openssl verify -CAfile ~/src/repsys/volumes/pki/intermediateCA/certs/intermediate.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/moto.busche-cnc.com.san.cert.pem
error 2 at 1 depth lookup: unable to get issuer certificate

# ok
openssl verify -CAfile ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/reports51.busche-cnc.com.san.cert.pem

openssl verify -CAfile ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/reports1.busche-cnc.com.san.cert.pem

openssl verify -CAfile ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/reports11.busche-cnc.com.san.cert.pem

openssl verify -CAfile ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/hrt-kors43.busche-cnc.com.san.cert.pem

openssl verify -CAfile ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/hrt-kors43.busche-cnc.com.san.cert.pem

openssl verify -CAfile ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/mgedo-srv-mach2.san.cert.pem

openssl verify -CAfile ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/reports-alb.busche-cnc.com.san.cert.pem

# Make the cert chain for nginx. this does not include the SAN certificates private key.
cat ~/src/repsys/volumes/pki/intermediateCA/certs/reports1.busche-cnc.com.san.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem  > ~/src/repsys/volumes/pki/intermediateCA/certs/server-chain/reports1.busche-cnc.com-ca-chain-bundle.cert.pem

cat ~/src/repsys/volumes/pki/intermediateCA/certs/reports51.busche-cnc.com.san.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem  > ~/src/repsys/volumes/pki/intermediateCA/certs/server-chain/reports51.busche-cnc.com-ca-chain-bundle.cert.pem


# This is the format needed for the Niagara system.
# Make the key server cert chain for Niagara. This needs to include the certificates private key generated from Niagara.
cat ~/src/repsys/volumes/pki/intermediateCA/certs/hrt-kors43.busche-cnc.com.san.cert.pem ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain/ca-chain-bundle.cert.pem  > ~/src/repsys/volumes/pki/intermediateCA/certs/server-chain/hrt-kors43.busche-cnc.com-ca-chain-bundle.cert.pem

# note: make sure private key has a carriage return at the end before concatenating it with the certificate bundle.
cat ~/src/repsys/volumes/pki/intermediateCA/private/hrt-kors43.busche-cnc.com.key.pem ~/src/repsys/volumes/pki/intermediateCA/certs/server-chain/hrt-kors43.busche-cnc.com-ca-chain-bundle.cert.pem  > ~/src/repsys/volumes/pki/intermediateCA/certs/niagara/key-hrt-kors43-ca-chain-bundle.cert.pem


# Apache Certificate installation process
pushd ~/src/repsys/volumes/pki/intermediateCA/certs
sudo mkdir -p /usr/local/apache2/conf/ssl.crt/
sudo mkdir -p /usr/local/apache2/conf/ssl.key/
# moto home and work
sudo cp moto.busche-cnc.com.san.cert.pem /usr/local/apache2/conf/ssl.crt/
ls -al /usr/local/apache2/conf/ssl.crt/
# devcon2
sudo cp devcon2.busche-cnc.com.san.cert.pem /usr/local/apache2/conf/ssl.crt/
# frt-kors43
sudo cp frt-kors43.busche-cnc.com.san.cert.pem /usr/local/apache2/conf/ssl.crt/

ls -al /usr/local/apache2/conf/ssl.crt/
# ca-chain-bundle
pushd ~/src/repsys/volumes/pki/intermediateCA/certs/ca-chain
sudo cp ca-chain-bundle.cert.pem /usr/local/apache2/conf/ssl.crt/
ls -al /usr/local/apache2/conf/ssl.crt/

# copy server key
pushd ~/src/repsys/volumes/pki/intermediateCA/private
# moto home and work
sudo cp moto.busche-cnc.com.san.key.pem /usr/local/apache2/conf/ssl.key/
ls -al /usr/local/apache2/conf/ssl.key/
# devcon2
sudo cp devcon2.busche-cnc.com.san.key.pem /usr/local/apache2/conf/ssl.key/
ls -al /usr/local/apache2/conf/ssl.key/

# Instructions for adding our certificate chain to Windows computers

# Our Mach2 servers use SSL certificates that we have generated from our PKI.  We must add the certificate chain to each computer that access the Mach2 servers so the user does not have to bypass a security warning screen.  There are several ways to do this.  

## add to local Windows trust store

certmgr
# add ca.cert.pem to trusted root certificate authorities
# add intermediate.cert.pem to intermediate certificate authorities

## add to Windows trust store using GPO

**[Not quite what we want but close](https://support.securly.com/hc/en-us/articles/206688537-How-to-push-the-Securly-SSL-certificate-with-Active-Directory-GPO
)**

# In our PKI we have an intermediate as well as a root CA so we must add a second step to import our intermediate certificate into the trusted intermediate certificate store like we did using certmgr in the previous section.

# add to chrome trust store

```bash
get ca-chain-bundle.cert.pem 
run chrome
type chrome://settings/certificates
go to Authorities tab
import certs
select PEM certificate chain from the dropdown.
select ca-chain-bundle.cert.pem
select trust websites
notice new authority entry and select it
verify both the root and intermediate certificates are installed.

# verify

certutil -d sql:$HOME/.pki/nssdb -L
Certificate Nickname                                         Trust Attributes
SSL,S/MIME,JAR/XPI

Root CA - Mobex Global                                       CT,c,c
Intermediate CA - Mobex Global                               ,,  

# add to Linux openssl trust store

# <https://ubuntu.com/server/docs/security-trust-store>
# <https://manpages.ubuntu.com/manpages/lunar/en/man8/update-ca-certificates.8.html>
# Installing a certificate in PEM form
# Assuming a PEM-formatted root CA certificate is in local-ca.crt, follow the steps below to install it.

# Note: It is important to have the .crt extension on the file, otherwise it will not be processed. Can only import one certificate at a time.

pushd /home/brent/src/repsys/volumes/pki/intermediateCA/certs
mkdir ~/certs
cp intermediate.cert.pem ~/certs
cp ca-chain/ca-chain-bundle.cert.pem ~/certs
pushd /home/brent/src/repsys/volumes/pki/rootCA/certs
cp ca.cert.pem ~/certs
sudo mount -t cifs //alb-utl.busche-cnc.com/Downloads /mnt/winshare -o username=bgroves
sudo cp ~/certs/ /mnt/winshare/

sudo apt-get install -y ca-certificates
sudo cp intermediate.cert.pem /usr/local/share/ca-certificates/intermediate.crt
pushd /home/brent/src/repsys/volumes/pki/rootCA/certs
sudo cp ca.cert.pem /usr/local/share/ca-certificates/ca.cert.crt
ls -al /usr/local/share/ca-certificates/ca.cer*
ls -al /usr/local/share/ca-certificates/int*
sudo update-ca-certificates

Importing into legacy system store:
I already trust 144, your new list has 139
Certificate added: C=US, S=Indiana, L=Albion, O=Mobex Global, CN=Root CA
Certificate added: C=US, S=Indiana, O=Mobex Global, CN=Intermediate CA
2 new root certificates were added to your trust store.
Import process completed.

Importing into BTLS system store:
I already trust 139, your new list has 139
Certificate added: C=ES, CN=Autoridad de Certificacion Firmaprofesional CIF A62634068
Certificate added: C=US, S=Indiana, L=Albion, O=Mobex Global, CN=Root CA
Certificate added: C=US, S=Indiana, O=Mobex Global, CN=Intermediate CA
3 new root certificates were added to your trust store.
Import process completed.
Done
done.

# verify

ls /etc/ssl/certs/ca*
ls /etc/ssl/certs/int*
sudo nvim /etc/ssl/certs/ca-certificates.crt

After this point you can use Ubuntu’s tools like curl and wget to connect to local sites.

# nginx ingress example
- **[K8s Ingress installation](../../k8s/ingress-lb-install.md)**


# Verify certificate is being used
openssl s_client -showcerts -connect reports11:443 -servername reports11 -CApath /etc/ssl/certs -


# Linux
<https://manpages.ubuntu.com/manpages/lunar/en/man8/update-ca-certificates.8.html>
openssl s_client -connect moto.busche-cnc.com:443 -CApath /etc/ssl/certs -
openssl s_client -connect frt-kors43.busche-cnc.com:443 -CApath /etc/ssl/certs


pushd /home/brent/src/repsys/volumes/pki/intermediateCA/certs/ca-chain
curl -v --cacert ca-chain-bundle.cert.pem https://frt-kors43.busche-cnc.com
curl -v https://frt-kors43.busche-cnc.com

curl -v --cacert ca-chain-bundle.cert.pem https://moto.busche-cnc.com
or
<https://serverfault.com/questions/485597/default-ca-cert-bundle-location>
Running curl with strace might give you a clue.
strace curl <https://moto.busche-cnc.com> |& grep open
openat(AT_FDCWD, "/home/brent/anaconda3/ssl/cacert.pem", O_RDONLY) = 6
openat(AT_FDCWD, "/etc/localtime", O_RDONLY|O_CLOEXEC) = 6

# deactivate an environment
conda deactivate
Now the openssl that comes with ubuntu is used
curl -L http://moto.busche-cnc.com
https://everything.curl.dev/http/hsts
curl -v --hsts hsts.txt https://frt-kors43.busche-cnc.com
curl -v --hsts hsts.txt http://frt-kors43.busche-cnc.com

# enable environment called "base", the default env from conda
conda activate base

# Windows and Mac
https://linuxize.com/post/how-to-mount-cifs-windows-share-on-linux/
add moto.busche-cnc.com to hosts file.
sudo mount -t cifs -o rw,username=paul //192.168.1.9/Users /mnt/win_share
bluebird12
sudo umount /mnt/win_share
C:\Windows\System32\drivers\etc

<https://docs.vmware.com/en/VMware-Horizon-7/7.13/horizon-scenarios-ssl-certificates/GUID-6E6211BC-3CA2-490F-B731-2B5210FF50E0.html>
You must import the root certificate and any intermediate certificates in the certificate chain into the Windows local computer certificate store.

If the TLS server certificate that you imported from the intermediate server is signed by a root CA that is known and trusted by the Connection Server host, and there are no intermediate certificates in your certificate chains, you can skip this task. Commonly used Certificate Authorities are likely to be trusted by the host.

Procedure
In the MMC console on the Windows Server host, expand the Certificates (Local Computer) node and go to the Trusted Root Certification Authorities > Certificates folder.
If your root certificate is in this folder, and there are no intermediate certificates in your certificate chain, skip to step 7.
If your root certificate is in this folder, and there are intermediate certificates in your certificate chain, skip to step 6.
If your root certificate is not in this folder, proceed to step 2.
Right-click the Trusted Root Certification Authorities > Certificates folder and click All Tasks > Import.
In the Certificate Import wizard, click Next and browse to the location where the root CA certificate is stored.
Select the root CA certificate file and click Open.
Click Next, click Next, and click Finish.
If your server certificate was signed by an intermediate CA, import all intermediate certificates in the certificate chain into the Windows local computer certificate store.
Go to the Certificates (Local Computer) > Intermediate Certification Authorities > Certificates folder.
Repeat steps 3 through 6 for each intermediate certificate that must be imported.
Restart the Connection Server service or Security Server service to make your changes take effect.

<https://www.namecheap.com/support/knowledgebase/article.aspx/9774/2238/incomplete-certificate-chain-on-windows-servers/>
<https://curl.se/docs/sslcerts.html>
Certificate Verification with Schannel and Secure Transport
If libcurl was built with Schannel (Microsoft's native TLS engine) or Secure Transport (Apple's native TLS engine) support, then libcurl will still perform peer certificate verification, but instead of using a CA cert bundle, it will use the certificates that are built into the OS. These are the same certificates that appear in the Internet Options control panel (under Windows) or Keychain Access application (under OS X). Any custom security rules for certificates will be honored.

Schannel will run CRL checks on certificates unless peer verification is disabled. Secure Transport on iOS will run OCSP checks on certificates unless peer verification is disabled. Secure Transport on OS X will run either OCSP or CRL checks on certificates if those features are enabled, and this behavior can be adjusted in the preferences of Keychain Access.


https://www.cloudflare.com/learning/ssl/what-is-sni/
## What is SNI? How TLS server name indication works
SNI, or Server Name Indication, is an addition to the TLS encryption protocol that enables a client device to specify the domain name it is trying to reach in the first step of the TLS handshake, preventing common name mismatch errors.
