https://httpd.apache.org/docs/2.4/ssl/ssl_faq.html

How do I speak HTTPS manually for testing purposes?
While you usually just use

$ telnet localhost 80
GET / HTTP/1.0
or simple testing of Apache via HTTP, it's not so easy for HTTPS because of the SSL protocol between TCP and HTTP. With the help of OpenSSL's s_client command, however, you can do a similar check via HTTPS:

$ openssl s_client -connect localhost:443 -state -debug
GET / HTTP/1.0

Before the actual HTTP response you will receive detailed information about the SSL handshake. For a more general command line client which directly understands both HTTP and HTTPS, can perform GET and POST operations, can use a proxy, supports byte ranges, etc. you should have a look at the nifty cURL tool. Using this, you can check that Apache is responding correctly to requests via HTTP and HTTPS as follows:

$ curl http://localhost/
$ curl https://localhost/

Why does the connection hang when I connect to my SSL-aware Apache server?
This can happen when you try to connect to a HTTPS server (or virtual server) via HTTP (eg, using http://example.com/ instead of https://example.com). It can also happen when trying to connect via HTTPS to a HTTP server (eg, using https://example.com/ on a server which doesn't support HTTPS, or which supports it on a non-standard port). Make sure that you're connecting to a (virtual) server that supports SSL.

Why do I get ``Connection Refused'' messages, when trying to access my newly installed Apache+mod_ssl server via HTTPS?
This error can be caused by an incorrect configuration. Please make sure that your Listen directives match your <VirtualHost> directives. If all else fails, please start afresh, using the default configuration provided by mod_ssl.

Why are the SSL_XXX variables not available to my CGI & SSI scripts?
Please make sure you have ``SSLOptions +StdEnvVars'' enabled for the context of your CGI/SSI requests.

How can I switch between HTTP and HTTPS in relative hyperlinks?
Usually, to switch between HTTP and HTTPS, you have to use fully-qualified hyperlinks (because you have to change the URL scheme). Using mod_rewrite however, you can manipulate relative hyperlinks, to achieve the same effect.

RewriteEngine on
RewriteRule   "^/(.*)_SSL$"   "https://%{SERVER_NAME}/$1" [R,L]
RewriteRule   "^/(.*)_NOSSL$" "http://%{SERVER_NAME}/$1"  [R,L]
