https://www.golinuxcloud.com/generate-self-signed-certificate-openssl/
Steps required to create self signed certificate in Linux
The steps involved to generate self signed certificate include:

Generate private key server.key
Create Certificate Signing Request (CSR) server.csr
Sign the certificate signing request and generate self signed certificate server.crt

Create encrypted password file (Optional)
With openssl self signed certificate you can generate private key with and without passphrase.
If you use any type of encryption while creating private key then you will have to provide passphrase every time you try to access private key.
With the encrypted password file we can avoid entering the password when we create self signed certificate.

I have created a plain text file "mypass" with my "secret" passphrase
bash
echo secret > mypass
Using openssl enc I will encrypt mypass file and create an encrypted file mypass.enc
openssl enc -aes256 -pbkdf2 -salt -in mypass -out mypass.enc
TWVzc2lhaDEhJA==
cat mypass.enc
To decrypt the encrypted password file, we use below command:
I will create a new directory to store my certificates
pushd ~/src/ca
git@github.com:brentgroves/ca.git

Openssl create self signed certificate with passphrase
In this section I will share the examples to openssl create self signed certificate with passphrase but we will use our encrypted file mypass.enc to create private key and other certificate files.

pushd ~/src/ca/articles/generate-self-signed-certificate-openssl
# Generate private key
We need to generate private key which will use in next steps to create Certificate Signing Request (CSR)
In this example we will create private key with 3DES encryption.
You can also choose any other encyption.

openssl genrsa -des3 -passout file:mypass.enc -out server.key 4096
Generating RSA private key, 4096 bit long modulus (2 primes)
...................................................................................................................................++++
....................................++++
e is 65537 (0x010001)
note: DES is a symmetric-key algorithm based on a Feistel network. As a symmetric key cipher.  
RSA algorithm is an asymmetric cryptography algorithm.
HINT: In this example I have used -passout with file:<filename>, but you can also use pass:<passphrase>, env:<variable>, fd:<number>. You can read more about these options: Network Security with OpenSSL. if you do not use -passout option, openssl generate private key command would prompt for the passphrase before generating private key.

# decrypt
openssl enc -aes256 -pbkdf2 -salt -d -in mypass.enc

# create csr
openssl req -new -key server.key -out server2.csr -passin file:mypass.enc -config custom_openssl.cnf



