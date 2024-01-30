yum install nginx
yum install mariadb
systemctl start php-fpm
systemctl start mariadb
systemctl start nginx
systemctl enable php-fpm nginx mariadb
vim /etc/nginx/conf.d/default.conf             
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
cd ..
rm -rf latest.zip wordpress/
pwd (check the path of the file and and ensure that it is the correct path in the configuration file
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




