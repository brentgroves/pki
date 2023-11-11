https://www.golinuxcloud.com/things-to-consider-when-creating-csr-openssl/
I have now covered multiple tutorials on working with openssl certificates. But there is one question where I get a lot of questions where certificate doesn't work due to incorrect entries in Certificate Signing Request.
So I have decided to create a dedicated tutorial to explain why CSR is important, and things to consider when writing a CSR.
Problems which you can face with incorrect CSR
Writing a CSR is the most crucial part of generating a certificate. If your CSR is not proper then:
RootCA may fail to sign the certificate
Your MTLS authentication will not work with TCP handshake error
You will end up creating multiple certificates for each host if you are not familiar with SAN
Your X.509 extensions will not be properly added
and many more ..
Important points to consider when creating CSR
The openssl command will by default consider /etc/ssl/openssl.cnf as the configuration file unless you specify your own configuration file using -config.
The req_distinguished_name field is used to get the details which will be asked while generating the CSR. You can alter this section inside the openssl.cnf and add the default values, modify the conditions such as min and max allowed characters etc
There are different policy sections available in the openssl.cnf. The policy_anything is normally used for self-signed certificates where all the fields except commonName are optional.
The policy_match section is used to generate RootCA certificates. If you are planning to use this RootCA certificate to sign any server or client certificate, then the respective sections marked as match must be same between RootCA and server or client certificate. In case you provide your stateOrProvinceName as Karnataka in RootCA and KARNATAKA in server certificate then the signing will fail as both will be considered as different values.
commonName is used for MTLS communications. The commonName must match the HOSTNAME or FQDN of the server on the server certificate and client on the client certificate. So if we have apache-client.example.com sending request to apache-server.example.com with MTLS authentication then the server certificate should have commonName as apache-server.example.com and client certificate should have apache-client.example.com
To consider High Availability and Load balancing, in IT organizations we use single FQDN mapped to multiple IP Addresses so in such case we prefer to use SAN certificates. So an -extfile param can be used with openssl command to provide the list of IP Address which would be validated for respective certificate. In such case you can provide the server's domain name as commonName while generating the CSR.
cat custom_openssl.cnf
distinguished_name = req_distinguished_name

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
countryName_default             = IN
countryName_min                 = 2
countryName_max                 = 2
stateOrProvinceName             = State or Province Name (full name) ## Print this message
stateOrProvinceName_default     = KARNATAKA ## This is the default value
localityName                    = Locality Name (eg, city) ## Print this message
localityName_default            = BANGALORE ## This is the default value
0.organizationName              = Organization Name (eg, company) ## Print this message
0.organizationName_default      = GoLinuxCloud ## This is the default value
organizationalUnitName          = Organizational Unit Name (eg, section) ## Print this message
organizationalUnitName_default  = Admin ## This is the default value
commonName                      = Common Name (eg, your name or your server hostname) ## Print this message    
commonName_max                  = 64
emailAddress                    = Email Address ## Print this message
emailAddress_max                = 64

RootCA Certificate CSR Example
We also need to write a CSR when creating our own RootCA certificate. If you are not providing your own openssl.cnf then by default /etc/ssl/openssl.cnf will be considered.

Now by default RootCA certificate follows below below guidelines from /etc/ssl/openssl.cnf:
policy          = policy_match

# For the CA policy
[ policy_match ]
countryName             = match
stateOrProvinceName     = match
organizationName        = match
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

## gen another private key with the mypass.enc file from generate-self-signed-certificate-openssl
openssl genrsa -des3 -passout file:mypass.enc -out server.key
# Next let us try to generate CSR using this custom configuration file:
openssl req -new -key server.key -out server.csr -config custom_openssl.cnf

This would mean that both RootCA and server/client certificate must have same countryName, stateOrProvinceName and organizationName or else the signing will fail.

# Let us take a look at this with a practical example:

Let me generate my RootCA certificate:

Let me generate my RootCA certificate:

bash

[root@controller certs]# openssl genrsa -out ca.key 4096

[root@controller certs]# openssl req -new -x509 -days 365 -key ca.key -out cacert.pem



