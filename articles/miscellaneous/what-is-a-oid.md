https://www.encryptionconsulting.com/education-center/what-is-an-oid#:~:text=An%20OID%2C%20or%20Object%20Identifier,to%20each%20CA's%20certificate%20policy.
Object Identifiers (OIDs) are like the Internet domain name space, organizations that need such an identifier may have a root OID assigned to them. They can thus create their own sub OIDs much like they can create subdomains. A large and standardized set of OIDs already exists.

An OID corresponds to a node in the “OID tree” or hierarchy, which is formally defined using the ITU’s OID standard, X.660. The root of the tree contains the following three arcs:

What is an Object Identifier (OID)?
An OID, or Object Identifier, can be applied to each CPS (Certificate Practice statement). The OID is an identifier that is tied to the CPS or, if multiple policies are defined, to each CA’s certificate policy.

Object Identifiers are controlled by IANA and you need to register a Private Enterprise Number (PEN), or OID arc under 1.3.6.1.4.1 namespace. Here is the PEN registration page: https://pen.iana.org/pen/PenApplication.page

When acquired, your OID namespace will look as follows: 1.3.6.1.4.1.{PENnumber}. You can assign certificate policies under your private namespace, for example:

1.3.6.1.4.1.{PENnumber}.1.1 – Smart Card issuance policy
1.3.6.1.4.1.{PENnumber}.1.2 – Digital signature certificate issuance policy
1.3.6.1.4.1.{PENnumber}.1.3 – Encryption certificate with key archival issuance policy
For general purpose CAs, you can use a universal Object Identifier with the value 2.5.29.32.0. This identifier means “All Issuance Policies” and is a sort of wildcard policy. Any policy will match this identifier during certificate chain validation.

