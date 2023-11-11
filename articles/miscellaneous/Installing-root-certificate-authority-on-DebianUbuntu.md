https://gist.github.com/corford/9a206664bb8278c8243821d236665d94

# mkdir /usr/share/ca-certificates/company
# [ copy Root CA cert to /usr/share/ca-certificates/company NOTE: file must be in PEM format and end in .crt ]
# dpkg-reconfigure ca-certificates
# openssl verify -CApath /path/to/root_ca /path/to/end-entity-certificate.pem

