Mach2 install attempts.

1. sam generated a csr and I signed it. this cert had no san or intermediate cert. Installed the root cert on Alb-utl.  From Chrome get certificate is not valid.
2. Sam generated a certificate and exported the private key and certificate. I used the private key,sam.key.pem to create a CSR and signed it with our intermediate certificate.
Attempt #2 worked using sams.san.cert.pem

# Niagara Certificate Process

## For each Mach2 server

1. Ask Sam to generate a certificate and export the private key and certificate.
2. Use the private key to create a CSR and sign it with our intermediate certificate.
3. Give SAN certificate to Sam to use with Mach2.

Sam generated a certificate and exported the private key and certificate. I used the private key,sam.key.pem to create a CSR and signed it with our intermediate certificate.

com.linamar.frt-kors43

Putd%$4pTE9!zp
