yum install nginx
yum install mariadb
systemctl start php-fpm
systemctl start mariadb
systemctl start nginx
systemctl enable php-fpm nginx mariadb
vim /etc/nginx/conf.d/default.conf

Categories
How to install Wordpress using Nginx

To Install WordPress using Nginx Web Server
In this article we will learn how to install WordPress using Nginx in CentOS. Nginx is compiled as a package in repositories and can be installed with apt-get Package Utility which supports Virtual Hosts like Apache and PHP files.

To Install Nginx Web Server
Ezoic

For Redhat Based
Run the following command to download the rpm package of nginx and then install it.

[root@linuxhelp~]#  wget http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
--2016-07-30 15:12:05--  http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
Resolving nginx.org (nginx.org)... 95.211.80.227, 206.251.255.63, 2001:1af8:4060:a004:21::e3
Connecting to nginx.org (nginx.org)|95.211.80.227|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4680 (4.6K) [application/x-redhat-package-manager]
Saving to: ‘ nginx-release-centos-7-0.el7.ngx.noarch.rpm’ 

100%[=============================================================> ] 4,680       --.-K/s   in 0.004s  

2016-07-30 15:12:07 (1.16 MB/s) - ‘ nginx-release-centos-7-0.el7.ngx.noarch.rpm’  saved [4680/4680]
Run the following command to add the rpm packages.

[root@linuxhelp~]# rpm -Uvh nginx-release-centos-7-0.el7.ngx.noarch.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:nginx-release-centos-7-0.el7.ngx ################################# [100%]
Now install the Nginx packages.

Ezoic
[root@linuxhelp~]# yum install nginx
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.dhakacom.com
 * extras: centos-hcm.viettelidc.com.vn
 * updates: centos-hcm.viettelidc.com.vn
updates/7/x86_64/primary_db                                                     | 5.7 MB  00:00:07     
Resolving Dependencies
-->  Running transaction check
--->  Package nginx.x86_64 1:1.10.1-1.el7.ngx will be installed
.
.
.
 Installing : 1:nginx-1.10.1-1.el7.ngx.x86_64                                                     1/1 
Thanks for using nginx!
Please find the official documentation for nginx here:
* http://nginx.org/en/docs/
Commercial subscriptions for nginx are available on:
* http://nginx.com/products/
----------------------------------------------------------------------
  Verifying  : 1:nginx-1.10.1-1.el7.ngx.x86_64                                                     1/1 

Installed:
  nginx.x86_64 1:1.10.1-1.el7.ngx                                                                      
Complete!
Ezoic

For Ubuntu based
Run the following command to install Nginx in Ubuntu.

root@linuxhelp~$ sudo apt-get install nginx
To install database
Run the following command to install the necessary packages for WordPress.
For Redhat based

[root@linuxhelp~]# yum install php-fpm php-mysql mariadb-server
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.dhakacom.com
 * extras: centos-hcm.viettelidc.com.vn
 * updates: centos-hcm.viettelidc.com.vn
Resolving Dependencies
-->  Running transaction check
--->  Package mariadb-server.x86_64 1:5.5.35-3.el7 will be updated
--->  Package mariadb-server.x86_64 1:5.5.47-1.el7_2 will be an update
--->  Package php-pdo.x86_64 0:5.4.16-36.1.el7_2.1 will be installed
-->  Running transaction check
--->  Package libzip.x86_64 0:0.10.1-8.el7 will be installed
.
.
.
Installed:
  php-fpm.x86_64 0:5.4.16-36.1.el7_2.1              php-mysql.x86_64 0:5.4.16-36.1.el7_2.1             

Updated:
  mariadb-server.x86_64 1:5.5.47-1.el7_2                                                               
Complete!
For Ubuntu based
Utilise the following command to install the php5 packages in Ubuntu.

Ezoic
root@linuxhelp~$ apt-get install php5 php5-fpm mysql-server php5-mysql
To configure WordPress
Open the config file and add the following contents in location category.

[root@linuxhelp~]# vim /etc/nginx/conf.d/default.conf
location / {
        try_files $uri $uri/ /index.php$is_args$args 
        }
The final config file appear as follows.

server {
    listen       80 
    server_name  localhost 
        root   /usr/share/nginx/html 
        index  index.html index.htm index.php 
location / {
try_files $uri $uri/ /index.php$is_args$args 
}
    error_page   500 502 503 504  /50x.html 
    location = /50x.html {
        root   /usr/share/nginx/html 
    }
   location ~ .php$ {
        fastcgi_pass   127.0.0.1:9000 
        fastcgi_index  index.php 
        fastcgi_param  SCRIPT_FILENAME  $request_filename 
        include        fastcgi_params 
    }
}
(make these changes)
vim /etc/php-fpm.d/www.conf
user = nginx
.
group = nginx
(make these changes)
cd /usr/share/nginx/html
rm -rf * ( clear all in the html directory)
wget http://wordpress.org/latest.zip
unzip latest.zip
cd wordpress
mv * ../
cp wp-config-sample.php wp-config.php
vim wp-config.php
define(' DB_NAME' , ' wordpress' ) 
define(' DB_USER' , ' wordpressuser' ) 
define(' DB_PASSWORD' , ' root' ) 
define(' DB_HOST' , ' localhost' ) 
define(' DB_CHARSET' , ' utf8' ) 
define(' DB_COLLATE' , ' ' )
systemctl restart nginx
systemctl restart php-fpm
systemctl restart mariadb


 http:/ip adress ( to run the wordpress on website)

![nazil wordpress-1](https://github.com/Muhammednaziln/Muhammednaziln/assets/156998948/43bb3a09-ec0d-47a8-97b2-82e456dabe3a)




