https://serverfault.com/questions/749468/cant-connect-to-server-running-apache-over-port-80

This is a networking issue, not an Apache issue. As you indicated, you can access the page using "curl" when run locally on the web server, but not from a browser on the desktop over the network. Check routing and firewalls between the desktop and the server.

You could try using nmap from your desktop to confirm if port 80 is accessible over the network, but not responding:

nmap -sS -O -p80 ip.of.your.server/32
Share
Edit
Follow
answered Jan 15, 2016 at 19:57
Mark Stosberg's user avatar
Mark Stosberg
3,9012424 silver badges2828 bronze badges
1
Yup. I missed that curl actually returns the page. – 
Reut Sharabani
 Jan 15, 2016 at 20:08
2
iptables. Apparently -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT is not the same as -A INPUT -p tcp -m tcp --dport 80 -j accept – 
Kisaragi
 Jan 15, 2016 at 20:15
Add a comment
4

tcp6 indicates ipv6 usage.

Either change the settings to use ipv4 by using:

Listen 0.0.0.0:80
And not

Listen 80
Or try curl localhost or curl ::1.