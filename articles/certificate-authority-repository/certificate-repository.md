https://eca.orc.com/understanding-the-repository/
https://www.openssl.org/docs/man3.0/man5/x509v3_config.html
caRepository in AIA

https://rpki.readthedocs.io/en/latest/
https://krill.docs.nlnetlabs.nl/en/stable/index.html
https://krill.docs.nlnetlabs.nl/en/stable/docker.html

Using Krill, you can run your own RPKI Certificate Authority as a child of one or more parent CAs, usually a Regional Internet Registry (RIR) or National Internet Registry (NIR). With Krill you can run under multiple parent CAs seamlessly and transparently. This is especially convenient if your organisation holds address space in several RIR regions, as it can all be managed as a single pool.
Krill can also act as a parent for child CAs. This means you can delegate resources down to children of your own, such as business units, departments, members or customers, who, in turn, manage ROAs themselves.
Lastly, Krill features a publication server so you can either publish your certificate and ROAs with a third party, such as your NIR or RIR, or you publish them yourself. Krill can be managed with a web user interface, from the command line and through an API.


https://cabforum.org/
CA browser forum
https://www.rfc-editor.org/rfc/rfc6481.html
https://www.arin.net/resources/manage/rpki/hybrid/

The certificate repository is a secured stronghold of all PKI-related components such as encryption keys, certificates of different types, and Certificate Signing Requests (CSRs). Certificate components are required for Alteon to supply SSL offloading services and client authentication.
https://guides.co/g/entrust-1492151/43112
Certificate Repositories And Certificate Distribution
As mentioned earlier in this paper, the CA acts as a trusted third-party issuing certificates to users. Businesses also must distribute those certificates so they can be used by applications. Certificate repositories store certificates so that applications can retrieve them on behalf of users. The term repository refers to a network service that allows for distribution of certificates.Over the past few years, the consensus in the information technology industry is that the best technology for certificate repositories is provided by directory systems that are LDAP (Lightweight Directory Access Protocol)-compliant. LDAP defines the standard protocol to access directory systems.Several factors drive this consensus position:
storing certificates in directories and having applications retrieve certificates on behalf of users provides the transparency required for use in most businesses
many directory technologies supporting LDAP can be scaled to:
support a very large number of entries
respond efficiently to search requests due to their information storage and retrieval methods, and
be distributed throughout the network to meet the requirements of even the most highly-distributed organizations


In addition, the directories that support certificate distribution can store other organizational information. As discussed in the next section, the PKI can also use the directory to distribute certificate revocation information.
Lets Encrypt 
To understand how the technology works, let’s walk through the process of setting up https://example.com/ with a certificate management agent that supports Let’s Encrypt.

There are two steps to this process. First, the agent proves to the CA that the web server controls a domain. Then, the agent can request, renew, and revoke certificates for that domain.

Domain Validation
Let’s Encrypt identifies the server administrator by public key. The first time the agent software interacts with Let’s Encrypt, it generates a new key pair and proves to the Let’s Encrypt CA that the server controls one or more domains. This is similar to the traditional CA process of creating an account and adding domains to that account.

To kick off the process, the agent asks the Let’s Encrypt CA what it needs to do in order to prove that it controls example.com. The Let’s Encrypt CA will look at the domain name being requested and issue one or more sets of challenges. These are different ways that the agent can prove control of the domain. For example, the CA might give the agent a choice of either:
Provisioning a DNS record under example.com, or
Provisioning an HTTP resource under a well-known URI on http://example.com/

https://pixelrobots.co.uk/2021/06/externaldns-and-azure-kubernetes-service-aks/
The how to guide
Before you go ahead and install ExternalDNS on to your cluster you need to set some variables that will be used later. First you need to get your tenant ID and subscription ID. If you have not already login to your azure subscription using the following.
