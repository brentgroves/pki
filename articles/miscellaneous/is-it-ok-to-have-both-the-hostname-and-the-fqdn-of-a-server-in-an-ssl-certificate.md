https://security.stackexchange.com/questions/260451/is-it-ok-to-have-both-the-hostname-and-the-fqdn-of-a-server-in-an-ssl-certific

A typical browser would probably not allow a secure connection to a host without a FQDN without displaying a warning message.  The browser wants to make sure its accessing the server the certificate was issued for.  If the certicate was issued for frt-kors43 it would not know for sure if it were accessing frt-kors43.google.com or frt-kors.busche-cnc.com because of the /etc/hosts file or automatic concatenation of a DNS suffix by the network settings. The whole domain name business and CA process is geared to verify the browser is accessing the FQDN securely so how could GoDaddy or CPanel issue a certificate to secure just a host name.  If we really wanted to bypass this warning to access https://frt-kors43 without getting a warning message we would have to start Chrome with the flag to bypass the security warning message for all URLs. 

On a company intranet, it is often more comfortable to access an internal webserver just by its hostname instead of providing the FQDN. As long as the hostname itself does not already equal a valid name on the internet, the automatic concatenation of the hostname and a DNS suffix, provided by the network settings, will be resolved to the intranet server successfully.

To not get certificate warnings, the certificate then needs to cover both names, the hostname and the FQDN of the server.

On ServerFault, there is a question that covers the technical feasibility of having both, the hostname and the FQDN in an SSL certificate (which technically is absolutely no problem, btw.). It partially drifted into a discussion, if it is OK to have both names in a certificate, that I'd like to commence here.

The main concern, that was mentioned, was trust. It would be necessary to work with the FQDN of a machine to trust it. Assuming correctly configured DNS suffixes, how can the trust be compromised, when using just the hostnames? Is it only a problem with multiple DNS suffixes (ambiguity)?

What about a default DNS suffix set on the clients? That would automatically "upgrade" the host name to a FQDN. – 
Robert
 Mar 18, 2022 at 13:21 
@Robert This does already happen "under the hood" as described in my first paragraph. But this will only affect the DNS query, not what will be visible in the address bar of the browser. – 
stackprotector
 Mar 18, 2022 at 14:06
1
Note that the solution described in the linked question on ServerFault is only possible if you run your own CA and your CA cert is installed on your users' systems. It is unlikely that a widely trusted CA (such as Verisign, GeoTrust, etc.) will sign a certificate with just a hostname (and not a FQDN) in the CN or SAN. See security.stackexchange.com/questions/121163/… for more info. – 
mti2935
 Mar 18, 2022 at 14:15
Is the intranet only accesible from internal network? What actual TLD are you using for the FQDN? – 
Rodrigo Murillo
 Mar 18, 2022 at 17:03
@RodrigoMurillo Yes, .com. – 
stackprotector
 Mar 18, 2022 at 19:45
