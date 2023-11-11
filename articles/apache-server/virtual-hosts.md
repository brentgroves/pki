https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-22-04#step-5-setting-up-virtual-hosts-recommended

Step 5 — Setting Up Virtual Hosts (Recommended)
When using the Apache web server, you can use virtual hosts (similar to server blocks in Nginx) to encapsulate configuration details and host more than one domain from a single server. We will set up a domain called your_domain, but you should replace this with your own domain name.

Apache on Ubuntu 22.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory. While this works well for a single site, it can become unwieldy if you are hosting multiple sites. Instead of modifying /var/www/html, create a directory structure within /var/www for a your_domain site, leaving /var/www/html in place as the default directory to be served if a client request doesn’t match any other sites.

Create the directory for your_domain as follows:

sudo mkdir /var/www/moto.busche-cnc.com

Next, assign ownership of the directory to the user you’re currently signed in as with the $USER environment variable:

sudo chown -R $USER:$USER /var/www/moto.busche-cnc.com

The permissions of your web root should be correct if you haven’t modified your umask value, which sets default file permissions. To ensure that your permissions are correct and allow the owner to read, write, and execute the files while granting only read and execute permissions to groups and others, you can input the following command:

sudo chmod -R 755 /var/www/moto.busche-cnc.com
Next, create a sample index.html page using nano or your favorite editor:

code /var/www/moto.busche-cnc.com/index.html

<html>
    <head>
        <title>Welcome to Your_domain!</title>
    </head>
    <body>
        <h1>Success!  The your_domain virtual host is working!</h1>
    </body>
</html>

In order for Apache to serve this content, it’s necessary to create a virtual host file with the correct directives. Instead of modifying the default configuration file located at /etc/apache2/sites-available/000-default.conf directly, make a new one at /etc/apache2/sites-available/your_domain.conf:

code /etc/apache2/sites-available/moto.busche-cnc.com.conf
Add in the following configuration block, which is similar to the default, but updated for your new directory and domain name:

/etc/apache2/sites-available/your_domain.conf
<VirtualHost *:80>
    ServerAdmin bgroves@mobexglobal.com
    ServerName moto.busche-cnc.com
    ServerAlias www.moto.busche-cnc.com
    DocumentRoot /var/www/moto.busche-cnc.com
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
Listen 80
<VirtualHost *:80>
    ServerAdmin bgroves@mobexglobal.com
    ServerName moto
    ServerAlias www.moto
    DocumentRoot "/var/www/moto"
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:80>
    DocumentRoot "/www/example1"
    ServerName www.example.com

    # Other directives here
</VirtualHost>

Notice that we’ve updated the DocumentRoot to our new directory and ServerAdmin to an email that the your_domain site administrator can access. We’ve also added two directives: ServerName, which establishes the base domain that will match this virtual host definition, and ServerAlias, which defines further names that will match as if they were the base name.

Save and close the file when you are finished.
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using moto.BUSCHE-CNC.com. Set the 'ServerName' directive globally to suppress this message
sudo nvim /etc/apache2/apache2.conf
Then add the following line below the “# Global configuration” section using your server’s IP address. For example, the IP address of my server is 10.1.1.83 :

ServerName 10.1.1.83
Save your changes and exit nano, then reload the apache2 service with systemctl:

$ sudo systemctl reload apache2

Now enable the file with the a2ensite tool:

sudo a2ensite moto.busche-cnc.com.conf
Disable the default site defined in 000-default.conf:

sudo a2dissite 000-default.conf
Next, test for configuration errors:
sudo systemctl reload apache2

sudo apache2ctl configtest



