# try this
https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-apache-in-ubuntu-20-04
I had a passphrase on moto.busche-cnc.com.cert.pem
-nodes: This tells OpenSSL to skip the option to secure our certificate with a passphrase. We need Apache to be able to read the file, without user intervention, when the server starts up. A passphrase would prevent this from happening, since we would have to enter it after every restart.
Fill out the prompts appropriately. The most important line is the one that requests the Common Name. You need to enter either the hostname you’ll use to access the server by, or the public IP of the server. It’s important that this field matches whatever you’ll put into your browser’s address bar to access the site, as a mismatch will cause more security errors.
sudo nvim /etc/apache2/sites-available/moto.busche-cnc.com.conf

<VirtualHost *:443>
   ServerName moto.busche-cnc.com
   DocumentRoot /var/www/moto.busche-cnc.com

   SSLEngine on
   SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
   SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
</VirtualHost>

Be sure to update the ServerName line to however you intend to address your server. This can be a hostname, full domain name, or an IP address. Make sure whatever you choose matches the Common Name you chose when making the certificate.
sudo mkdir /var/www/moto.busche-cnc.com
sudo mkdir /var/www/moto.busche-cnc.com

echo -e "<h1>it worked</h1>" | sudo tee -a "/var/www/moto.busche-cnc.com/index.html"

sudo a2ensite moto.busche-cnc.com

sudo apache2ctl configtest
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using moto.busche-cnc.com. Set the 'ServerName' directive globally to suppress this message
Syntax OK

The first line is a message telling you that the ServerName directive is not set globally. If you want to get rid of that message, you can set ServerName to your server’s domain name or IP address in /etc/apache2/apache2.conf. This is optional as the message will do no harm.

sudo nvim /etc/apache2/apache2.conf
ServerName moto.busche-cnc.com
# Summary of how the Apache 2 configuration works in Debian:
# The Apache 2 web server configuration in Debian is quite different to
# upstream's suggested way to configure the web server. This is because Debian's
# default Apache2 installation attempts to make adding and removing modules,
# virtual hosts, and extra configuration directives as flexible as possible, in
# order to make automating the changes and administering the server as easy as
# possible.

# It is split into several files forming the configuration hierarchy outlined
# below, all located in the /etc/apache2/ directory:
#
#       /etc/apache2/
#       |-- apache2.conf
#       |       `--  ports.conf
#       |-- mods-enabled
#       |       |-- *.load
#       |       `-- *.conf
#       |-- conf-enabled
#       |       `-- *.conf
#       `-- sites-enabled
#               `-- *.conf
#

sudo apache2ctl configtest
Syntax OK
sudo systemctl reload apache2
sudo nvim /etc/apache2/sites-available/moto.busche-cnc.com.conf
At the bottom, create another VirtualHost block to match requests on port 80. Use the ServerName directive to again match your domain name or IP address. Then, use Redirect to match any requests and send them to the SSL VirtualHost. Make sure to include the trailing slash:
sudo nvim /etc/apache2/sites-enabled/000-default.conf 
000-default.conf contains this already so make sure to change * for FQDN
<VirtualHost *:80>
	ServerName moto.busche-cnc.com
	Redirect / https://moto.busche-cnc.com/
</VirtualHost>

sudo apachectl configtest
sudo systemctl reload apache2
curl -H 'Cache-Control: no-cache' http://www.example.com

https://www.rosehosting.com/blog/how-to-enable-https-protocol-with-apache-2-on-ubuntu-20-04/
https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-apache-in-ubuntu-18-04
https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/
https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-apache-in-ubuntu-22-04
https://hub.docker.com/_/httpd?tab=description
put the sed command into the dockerfile

https://gist.github.com/jaredhoyt/778724/495a2dda7a95e8c794b220b6bc6200f5ff9b5272
name based virtual hosting