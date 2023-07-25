**Magento Project**

The following section describes how to install Adobe Commerce and Magento Open Source 2.x on Ubuntu using nginx, PHP, and MySQL. Reference ( <https://devdocs.magento.com/guides/v2.3/install-gde/prereq/nginx.html>)

Install nginx

sudo apt -y install nginx

Check nginx running state

Sudo service nginx status

Install and configure php-fpm

Adobe Commerce and Magento Open Source require several PHP extensions to function properly. In addition to these extensions, you must also install and configure the php-fpm extension if you are using nginx.

To install and configure php-fpm 8.1:

sudo apt update

sudo apt install --no-install-recommends php8.1

sudo apt-get install php8.1-PACKAGE\_NAME

sudo apt-get install -y php8.1-cli php8.1-common php8.1-mysql php8.1-zip php8.1-gd php8.1-mbstring php8.1-curl php8.1-xml php8.1-bcmath php8.1-soap php8.1-intl

php -m

1\.Install php-fpm and php-cli:

apt-get -y install php8.1-fpm php8.1-cli

2\. Open the php.ini files in an editor:

vim /etc/php/8.1/fpm/php.ini

vim /etc/php/8.1/cli/php.ini

3\. Edit both files to match the following lines:

memory\_limit = 2G

max\_execution\_time = 1800

zlib.output\_compression = On

4\. Save and exit the editor.

5\.Restart the php-fpm service:

systemctl restart php8.1-fpm

Setting Up Composer for Dependency Management

curl -sS <https://getcomposer.org/installer> -o /tmp/composer-setup.php

HASH=`curl -sS [https://composer.github.io/installer.sig`](https://composer.github.io/installer.sig%60)

echo $HASH

Example output:

Output

55ce33d7678c5a611085589f1f3ddf8b3c52d662cd01d4ba75c0ee0459970c2200a51f492d557530c71c15d8dba01eae

php -r "if (hash\_file('SHA384', '/tmp/composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP\_EOL;"

You’ll see the following output:

Output

Installer verified

sudo php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer

You’ll receive output similar to this:

Output

All settings correct for using Composer

Downloading...

Composer (version 2.3.5) successfully installed to: /usr/local/bin/composer

Use it: php /usr/local/bin/composer

To test your installation, run:

Composer

Install and configure a MySQL server

sudo apt install mysql-server

sudo service mysql status

sudo ss -tap | grep mysql

-Change to the web server docroot directory or a directory that you have configured as a virtual host docroot. For this example, we’re using the Ubuntu default /var/www/html.

Download project databases SQL file from staging & login to mysql below command

Sudo mysql –u root –p

Create new database below command

Create database asv\_staging;

Use asv\_staging

Import downloaded sql file by below command make sure your inside of asv\_staging database

Source asv\_staging.sql (file path or location)

After successfully import database please exit.

Change the project staging url (<http://staging.asv.com.au/>) to local url (http://loc.asv.com/) same for b2b url as well by downloading core\_config\_data

Mysqldump –u root –p asv\_staging core\_config\_data > core\_config\_data.sql

Core config data sql file is available inside pwd open in VS code make modification accordingly 

Go to databases via terminal past complete core\_config\_data sql file after your modefication

cd /var/www/html/asv\_ecome/

Existing Magento Project 

Clone Repo from Bitbucket

git clone “Your project”

Run composer update to download lib file

Composer update

Install Redis server & elastic search

Download config file from staging server

Project root directory/app/etc config.php & env.php

Past them in respective directory in localhost & update database name in env.php file 

Fresh Magento Installation (Not recommended)

curl -sS <https://getcomposer.org/installer> | sudo php -- --install-dir=/usr/bin --filename=composer

composer create-project --repository=https://repo.magento.com/ magento/project-community-edition <install-directory-name>

<install-directory-name> = where to install, name.

Ex: magento


When prompted, enter your authentication keys. Your public key is your username; your private key is your password.

Go to below url. <https://commercemarketplace.adobe.com/customer/accessKeys/>

Public Key: ffaac2b9e26060febbxxxxxxxxxxxxxxxxxx (user)

Private Key: 52292b80d548ead89xxxxxxxxxxxxxxxx (password)

-Set read-write permissions for the web server group before you install the application. This is necessary so that the command line can write files to the file system.

cd /var/www/html/<magento install directory>

cd /var/www/html/magento

find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} +

find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} +

chown -R :www-data . # Ubuntu

Configure nginx

Add local url (http://loc.asv.com) inside hosts

Sudo nano /etc/hosts

Create /etc/nginx/sites-available and /etc/nginx/sites-enabled and then edit the http block inside /etc/nginx/nginx.conf and add this line & www-data for user

include /etc/nginx/sites-enabled/\*;

vim /etc/nginx/sites-available/magento 




upstream fastcgi\_backend { 

`  `server  unix:/run/php/php8.1-fpm.sock; 

} 

map $http\_host $MAGE\_RUN\_CODE {  
`     `loc.b2c default;  
`     `loc.b2b b2b\_store\_view;
` `}

server { 



`  `listen 80; 

`  `server\_name loc.asv.com; 

`  `set $MAGE\_ROOT /var/www/html/project\_root\_directory; 

`  `include /var/www/html/project\_root\_diretory/nginx.conf.sample; 

}

Download nginx.conf.sample & pub/index.php file form staging server & replace them

Create symlink by below command


|ln -s /etc/nginx/sites-available/magento /etc/nginx/sites-enabled|
| :- |


Restart nginx:

systemctl restart nginx

Complete Successfully

Run Magento command 

Sh deploy.sh

