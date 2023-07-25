How to Set Up Squid Proxy for Private Connections on Ubuntu 20.04

Step 1 — Installing Squid Proxy
sudo apt update
sudo apt install squid
systemctl status squid.service
sudo nano /etc/squid/squid.conf
  Search for http_access deny all change it to http_access deny all  
Step 2 — Securing Squid
sudo apt install apache2-utils
sudo htpasswd -c /etc/squid/passwords your_squid_username
enter user name and password.
sudo cat /etc/squid/passwords
sudo nano /etc/squid/squid.conf
Enter blue coloured Information on conf file as below:
#
# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS
#
include /etc/squid/conf.d/*
auth_param basic program /usr/lib/squid3/basic_ncsa_auth /etc/squid/passwords
auth_param basic realm proxy
acl authenticated proxy_auth REQUIRED
# Example rule allowing access from your local networks.
# Adapt localnet in the ACL section to list your (internal) IP networks
# from where browsing should be allowed
#http_access allow localnet
http_access allow localhost
http_access allow authenticated
# And finally deny all other access to this proxy
http_access deny all
…


sudo systemctl restart squid.service
sudo ufw allow 3128

Step 3 — Connecting through Squid

http
curl -v -x http://your_squid_username:your_squid_password@your_server_ip:3128 http://www.google.com/
https
curl -v -x http://your_squid_username:your_squid_password@your_server_ip:3128 http://www.google.com/

Notes: 
1.for AWS cloud open port on SG
2. this is for Ubuntu 20.04
