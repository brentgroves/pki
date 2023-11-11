https://superuser.com/questions/1254036/what-does-openssl-rsa-passin-passxxx-without-other-important-commands-do
openssl genrsa -des3 -passout pass:123 -out server.key 2048
# remove password
openssl rsa -passin pass:123 -in server.key -out server.key
# usage 1
openssl genrsa -des3 -passout pass:123 -out server.key 2048
openssl rsa -passin pass:123 -noout -text -in server.key
# usage 2
openssl genrsa -des3 -passout file:/home/brent/src/ca/mypass.enc -out server3.key 2048
openssl rsa -passin file:/home/brent/src/ca/mypass.enc -noout -text -in server3.key

https://www.golinuxcloud.com/generate-self-signed-certificate-openssl/#Create_encrypted_password_file_Optional
I have created a plain text file "mypass" with my "secret" passphrase
echo secret > mypass
Using openssl enc I will encrypt mypass file and create an encrypted file mypass.enc
openssl enc -aes256 -pbkdf2 -salt -in mypass -out mypass.enc
enter aes-256-cbc encryption password:
Verifying - enter aes-256-cbc encryption password:
As you see the content of the encrypted file is not readable any more. Now you can easily share this encrypted file to any user to generate ssl certificate
bash
cat mypass.enc
Salted__▒▒Y$▒V΃cQVȥ▒2ĺ)▒MS▒
To decrypt the encrypted password file, we use below command:
bash
openssl enc -aes256 -pbkdf2 -salt -d -in mypass.enc
enter aes-256-cbc decryption password:
secret

I will create a new directory to store my certificates

Openssl create self signed certificate with passphrase
In this section I will share the examples to openssl create self signed certificate with passphrase but we will use our encrypted file mypass.enc to create private key and other certificate files.

 

Generate private key
We need to generate private key which will use in next steps to create Certificate Signing Request (CSR)
In this example we will create private key with 3DES encryption.
You can also choose any other encyption.
bash

[root@centos8-1 certs]# openssl genrsa -des3 -passout file:mypass.enc -out server.key 4096
Generating RSA private key, 4096 bit long modulus (2 primes)
................................................++++
..++++
e is 65537 (0x010001)
HINT: In this example I have used -passout with file:<filename>, but you can also use pass:<passphrase>, env:<variable>, fd:<number>. You can read more about these options: Network Security with OpenSSL. if you do not use -passout option, openssl generate private key command would prompt for the passphrase before generating private key.

Create Certificate Signing Request (CSR) certificate
ALSO READ:
Many people miss most important points when they are creating a CSR. If you are not sure about what should be added for individual fields then I would recommend to read this article before you generate CSR:
Things to consider when creating CSR with OpenSSL

Next create a certificate signing request (server.csr) using the openssl private key (server.key).

This command will prompt for a series of things (country, state or province, etc.). Make sure that "Common Name" matches the registered fully qualified domain name of your Linux server (or your IP address if you don't have one). Alternatively you can also create SAN certification which will allow you to provide multiple Alternative Names in a single certificate.

bash

[root@centos8-1 certs]# openssl req -new -key server.key -out server.csr -passin file:mypass.enc
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:IN
State or Province Name (full name) []:KARNATAKA
Locality Name (eg, city) [Default City]:BENGALURU
Organization Name (eg, company) [Default Company Ltd]:GoLinuxCloud
Organizational Unit Name (eg, section) []:R&D
Common Name (eg, your name or your server's hostname) []:centos8-1
Email Address []:admin@golinuxcloud.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

Openssl verify certificate content
In this article we have create below certificates

server.key ⇒ Private Key
server.csr ⇒ Certificate Signing Request
server.crt ⇒ Self-signed certificate
You can view the content of self signed certificate and other files using openssl:

bash

# openssl rsa -noout -text -in server.key
# openssl req -noout -text -in server.csr
# openssl x509 -noout -text -in server.crt
  

