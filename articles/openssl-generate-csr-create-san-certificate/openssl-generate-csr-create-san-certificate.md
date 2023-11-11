https://www.golinuxcloud.com/openssl-generate-csr-create-san-certificate/
In this article we will learn the steps to create SAN Certificate using openssl generate csr with san command line and openssl sign csr with subject alternative name.

# Generate server private key
You can ignore this step if you already have a private key. For the sake of demonstration I am creating a new server private key.
bash
openssl genpkey -des3 -pass file:/home/brent/src/ca/mypass.enc -algorithm RSA -out ~/src/ca/intermediateCA/private/moto.busche-cnc.com.san.key.pem                     

# Openssl Generate CSR with SAN command line
Now to create SAN certificate we must generate a new CSR i.e. Certificate Signing Request which we will use in next step with openssl generate csr with san command line.
bash
openssl req -passin file:/home/brent/src/ca/mypass.enc \
        -config ~/src/ca/openssl_intermediate_for_san_certificate.cnf \
        -key ~/src/ca/intermediateCA/private/moto.busche-cnc.com.san.key.pem \
        -new -sha256 -out ~/src/ca/intermediateCA/csr/moto.busche-cnc.com.san.csr.pem

# dont use this one because the openssl_intermediate_for_san_certificate.cnf config file has the addext info
openssl req -new -subj "/C=GB/CN=foo" \
             -addext "subjectAltName = DNS:foo.co.uk" \
             -addext "certificatePolicies = 1.2.3.4" \
             -newkey rsa:2048 -keyout key.pem -out req.pem

## note set common name equal to a name in alt_names
[alt_names]
DNS.1 = moto.busche-cnc.com

# Openssl sign CSR with Subject Alternative Name
Next use the server.csr to sign the server certificate with -extfile <filename> using Subject Alternative Names to create SAN certificate
I am using my CA Certificate Chain and CA key from my previous article to issue the server certificate
The server certificate will be valid of 365 days and with sha256 algorithm
Since our CA key is encrypted with passphrase, I have used -passin to provide the passphrase, If I do not use this argument then the command will prompt for input passphrase.
In this command using openssl x509 we create SAN certificate server.cert.pem
bash
openssl x509 -req \
-passin file:/home/brent/src/ca/mypass.enc \
-in /home/brent/src/ca/intermediateCA/csr/moto.busche-cnc.com.san.csr.pem \
-CA /home/brent/src/ca/intermediateCA/certs/ca-chain-bundle.cert.pem \
-CAkey /home/brent/src/ca/intermediateCA/private/intermediate.key.pem \
-out /home/brent/src/ca/intermediateCA/certs/moto.busche-cnc.com.san.cert.pem \
-CAcreateserial -days 365 -sha256 \
-extfile /home/brent/src/ca/server_cert_ext.cnf

Signature ok
subject=C = US
Getting CA Private Key

# Openssl verify certificate content
After you create SAN certificate, next you can check the content of your server certificate to make sure openssl sign CSR with Subject Alternative Name was successful.
bash
openssl x509 -noout -text -in ~/src/ca/intermediateCA/certs/moto.busche-cnc.com.san.cert.pem
...
        X509v3 extensions:
            X509v3 Basic Constraints: 
                CA:FALSE
            Netscape Cert Type: 
                SSL Server
            Netscape Comment: 
                OpenSSL Generated Server Certificate
            X509v3 Subject Key Identifier: 
                54:98:4A:DD:1B:35:14:5D:E5:F8:0A:99:42:1F:A7:9F:A4:FE:CF:1B
            X509v3 Authority Key Identifier: 
                keyid:A5:F6:2D:4C:41:BE:F7:E8:10:E4:C8:01:3D:C9:E9:F7:EB:1C:75:18
                DirName:/C=US/ST=Indiana/L=Albion/O=Mobex Global/OU=Information Systems/CN=Root CA
                serial:2B:F3:60:B9:F0:55:E0:76:7F:C6:87:EC:FE:A8:02:3E:13:BB:F2:9F
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication
            X509v3 Subject Alternative Name: 
                IP Address:10.1.1.83, DNS:moto.busche-cnc.com

# full chain
cat ~/src/ca/intermediateCA/certs/moto.busche-cnc.com.san.cert.pem ~/src/ca/intermediateCA/certs/intermediate.cert.pem > ~/src/ca/intermediateCA/certs/moto.busche-cnc.com.san.intermediate.cert.pem

cat ~/src/ca/intermediateCA/certs/moto.busche-cnc.com.san.intermediate.cert.pem ~/src/ca/rootCA/certs/ca.cert.pem > ~/src/ca/intermediateCA/certs/moto.busche-cnc.com.san.chain.cert.pem

rm ~/src/ca/intermediateCA/certs/moto.busche-cnc.com.san.intermediate.cert.pem

https://tools.keycdn.com/ssl
No chain issues detected.
1. Subject CN: moto.busche-cnc.com > Issuer CN: Intermediate CA
2. Subject CN: Intermediate CA > Issuer CN: Root CA
3. Subject CN: Root CA > Issuer CN: Root CA

https://crt.sh/lintcert

# install in trust store
https://support.securly.com/hc/en-us/articles/360036106474-How-to-install-Securly-SSL-certificate-via-Group-Policy-