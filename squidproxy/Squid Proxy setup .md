**How to Set Up Squid Proxy for Private Connections on Ubuntu 20.04**

**Step 1 — Installing Squid Proxy**

sudo apt update

sudo apt install squid

systemctl status squid.service

sudo nano /etc/squid/squid.conf

`  `Search for http\_access deny all change it to http\_access deny all  

**Step 2 — Securing Squid**

sudo apt install apache2-utils

sudo htpasswd -c /etc/squid/passwords your\_squid\_username

enter user name and password.

sudo cat /etc/squid/passwords

sudo nano /etc/squid/squid.conf

Enter blue coloured Information on conf file as below:

\#

\# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS

\#

include /etc/squid/conf.d/\*

**auth\_param basic program /usr/lib/squid3/basic\_ncsa\_auth /etc/squid/passwords**

**auth\_param basic realm proxy**

**acl authenticated proxy\_auth REQUIRED**

\# Example rule allowing access from your local networks.

\# Adapt localnet in the ACL section to list your (internal) IP networks

\# from where browsing should be allowed

#http\_access allow localnet

http\_access allow localhost

**http\_access allow authenticated**

\# And finally deny all other access to this proxy

http\_access deny all

…


sudo systemctl restart squid.service

sudo ufw allow 3128

**Step 3 — Connecting through Squid**

http

curl -v -x http://your\_squid\_username:your\_squid\_password@your\_server\_ip:3128 <http://www.google.com/>

https

curl -v -x http://your\_squid\_username:your\_squid\_password@your\_server\_ip:3128 http://www.google.com/

Notes: 

1\.for AWS cloud open port on SG

2\. this is for Ubuntu 20.04
