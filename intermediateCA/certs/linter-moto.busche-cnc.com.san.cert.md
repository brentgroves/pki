https://crt.sh/lintcert
https://tools.keycdn.com/ssl 
https://github.com/zmap/zlint


# cablint	WARNING	Certificate does not include authorityInformationAccess. BRs require OCSP stapling for this certificate.
authorityInfoAccess = OCSP;URI:http://ocsp.busche-cnc.com/
authorityInfoAccess = caIssuers;URI:http://my.ca/ca.html
authorityInfoAccess = OCSP;URI:http://ocsp.example.com/,caIssuers;URI:http://myca.example.com/ca.cer

authorityInfoAccess = OCSP;URI:http://ocsp.example.com/
cablint	WARNING	Deprecated Netscape extension 2.16.840.1.113730.1.13 treated as opaque extension
cablint	WARNING	Deprecated Netscape extension 2.16.840.1.113730.1.1 treated as opaque extension
cablint	INFO	Name has deprecated attribute emailAddress
cablint	INFO	TLS Server certificate identified
x509lint	ERROR	no authorityInformationAccess extension
x509lint	ERROR	No OCSP over HTTP
x509lint	ERROR	No policy extension
x509lint	WARNING	No HTTP URL for issuing certificate
x509lint	INFO	Checking as leaf certificate
x509lint	INFO	Subject has a deprecated CommonName
x509lint	INFO	Unknown validation policy
zlint	ERROR	CAs MUST NOT issue certificates that have authority key IDs that include both the key ID and the issuer's issuer name and serial number
https://github.com/OpenVPN/easy-rsa/issues/417
zlint	ERROR	CAs SHALL NOT issue certificates with a subjectAltName extension or subject:commonName field containing a Reserved IP Address or Internal Name.
zlint	ERROR	OrganizationalUnitName is prohibited if...the certificate was issued on or after September 1, 2022
zlint	ERROR	Subscriber Certificate: authorityInformationAccess MUST be present.
zlint	ERROR	Subscriber Certificate: authorityInformationAccess MUST contain the HTTP URL of the Issuing CA's OSCP responder.
zlint	ERROR	Subscriber Certificate: certificatePolicies MUST be present and SHOULD NOT be marked critical.
zlint	ERROR	Subscriber certificates must contain at least one policy identifier that indicates adherence to CAB standards
zlint	WARNING	Subscriber certificates authorityInformationAccess extension should contain the HTTP URL of the issuing CA’s certificate
zlint	NOTICE	Check if certificate has enough embedded SCTs to meet Apple CT Policy
zlint	NOTICE	Subscriber Certificate: commonName is deprecated.

# no linter errors left
cablint	INFO	TLS Server certificate identified
x509lint	INFO	Checking as leaf certificate
zlint	NOTICE	Check if certificate has enough embedded SCTs to meet Apple CT Policy