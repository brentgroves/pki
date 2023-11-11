# change hostname to be a FQDN

<https://linux.how2shout.com/3-ways-to-change-hostname-on-ubuntu-20-04-lts-focal-fossa-linux/>
sudo hostname moto.busche-cnc.com

## set up virtual hosting

virtual hosting
<https://www.golinuxhub.com/2014/08/what-are-different-types-of-virtual-web/>

## Name based virtual Web hosting

This requires updates to a DNS server. I believe the problems I was having with virtual hosts on MOTO was with DNS and /etc/resolvd not recognizing moto.busche-cnc.com.  Maybe if I installed a DNS server I could get name based virtual web hosting to work from within busche.cnc.com domain.

## Port Based Virtual Web Hosting

I will use this one because the name based requires working with a DNS server.

## customize the httpd.conf file

<https://hub.docker.com/_/httpd?tab=description>
Configuration
To customize the configuration of the httpd server, first obtain the upstream default configuration from the container:
pushd ~/src/reports/.devcontainer/dockerfiles/httpd
$ docker run --rm brentgroves/httpd:1.0 cat /usr/local/apache2/conf/httpd.conf > ./conf/httpd.conf
You can then COPY your custom configuration in as /usr/local/apache2/conf/httpd.conf:

``` bash

FROM httpd:2.4
COPY ./config/httpd.conf /usr/local/apache2/conf/httpd.conf

```

## ssl

run script to update httpd.conf to load modules that support ssl.

./update-httpd.sh
The sed command could be added to the dockerfile.

Switch back to <https://www.golinuxhub.com/2014/08/what-are-different-types-of-virtual-web/>
Steps to configure a port based web hosting
I will create a path to demonstrate this scenario

``` bash
mkdir /var/www/port
cd /var/www/port/
Create a sample index file for testing purpose
cat index.html
<h1> PORT BASED WEB HOSTING <h1>
Welcome to Golinuxhub ####
```

## copy the certs in the dockerfile:
COPY ./certs /etc/httpd/conf.d/certs/

Referencing this doc https://www.golinuxcloud.com/openssl-create-client-server-certificate/
we are to put the virtual host definition in httpd.conf but doing this gave the following errors.
docker run --rm -ti -p80:80 --name 'httpd-test' brentgroves/httpd:1.0 /bin/bash
AH00526: Syntax error on line 144 of /usr/local/apache2/conf/extra/httpd-ssl.conf:
SSLCertificateFile: file '/usr/local/apache2/conf/server.crt' does not exist or is empty

## update the ssl config file
1 make a copy of the file
$ docker run --rm brentgroves/httpd:1.0 cat /usr/local/apache2/conf/extra/httpd-ssl.conf > ./conf/httpd-ssl.conf
2. make changes to the file.
https://httpd.apache.org/docs/2.4/mod/mod_ssl.html
The link contains the latest notes for all the directives included in httpd-ssl.conf
https://guides.dataverse.org/en/4.7/_downloads/ssl.conf
https://httpd.apache.org/docs/2.4/ssl/ssl_faq.html

# build image
pushd ~/src/reports/.devcontainer/dockerfiles/httpd

docker build --no-cache -t brentgroves/httpd:nossl . --progress=plain
docker build --no-cache -t brentgroves/httpd:ssl . --progress=plain

docker build -t brentgroves/httpd:1.0 . --progress=plain
docker run --rm -ti -p80:80 -p443:443 --name 'httpd-test' brentgroves/httpd:1.0 /bin/bash
docker run -dit --name my-running-app -p 80:80 brentgroves/httpd:1.0
docker run -dit --name https-app -p 80:80 -p 443:443 brentgroves/httpd:ssl
# test Apache
https://geekflare.com/apache-setup-ssl-certificate/
open httpd-tests.md and run tests

curl -k <https://moto>
curl <http://10.1.1.83>

netstat -ntlp
netstat -anp | grep 80

<https://www.golinuxcloud.com/openssl-create-client-server-certificate/>
<https://hub.docker.com/_/httpd?tab=description>
