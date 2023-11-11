https://httpd.apache.org/docs/2.4/mod/mod_ssl.html

Summary
This module provides SSL v3 and TLS v1.x support for the Apache HTTP Server. SSL v2 is no longer supported.

This module relies on OpenSSL to provide the cryptography engine.

Further details, discussion, and examples are provided in the SSL documentation.s

SSLCertificateChainFile is deprecated
SSLCertificateChainFile became obsolete with version 2.4.8, when SSLCertificateFile was extended to also load intermediate CA certificates from the server certificate file.
