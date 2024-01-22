yum install nginx
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
setenforce 0 (for disabled selinux)
yum -y install mariadb mariadb-server
systemctl start mariadb
systemctl enable mariadb
yum install -y https://rpms.remirepo.net/enterprise/remi-release-7.rpm
yum install -y --enablerepo=remi-php73 php php-fpm php-mysqlnd php-cli
vi /etc/php-fpm.d/www.conf
listen = 127.0.0.1:9000
vi /etc/nginx/conf.d/virtual.conf
server {
        server_name nazil.com;
        root /usr/share/nginx/html/nazil.local;
    location / {
        index index.html index.htm index.php;
    }
     location ~ \.php$ {
        include /etc/nginx/fastcgi_params;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME /usr/share/nginx/html/nazil.local$fastcgi_script_name;
   }
}
vi /etc/hosts
192.168.96.65 nazil.com
mkdir /usr/share/nginx/html/nazil.local
echo "<?php phpinfo(); ?>" > /usr/share/nginx/html/itzgeek.local/index.php
systemctl restart nginx
systemctl restart php-fpm
systemctl enable php-fpm

(to install php myadmin)

yum install --enablerepo=remi-php73 phpmyadmin
vi /etc/nginx/conf.d/phpMyAdmin.conf
server {
        listen   80;
        server_name phpmyadmin.nazil.local;
        root /usr/share/phpMyAdmin;
    location / {
        index  index.php;
    }
## Images and static content is treated different
        location ~* ^.+.(jpg|jpeg|gif|css|png|js|ico|xml)$ {
        access_log        off;
        expires           30d;
    }
    location ~ /\.ht {
        deny  all;
    }
    location ~ /(libraries|setup/frames|setup/libs) {
        deny all;
        return 404;
    }
    location ~ \.php$ {
        include /etc/nginx/fastcgi_params;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME /usr/share/phpMyAdmin$fastcgi_script_name;
    }
}
systemctl restart nginx
systemctl restart php-fpm
firewall-cmd --permanent --add-service=http
firewall-cmd --reload

(to install wordpress)

vi /etc/nginx/conf.d/wordpress.conf
server {
        listen 80;
        server_name wordpress.itzgeek.local;

        access_log /usr/share/nginx/wordpress.itzgeek.local/logs/access.log;
        error_log /usr/share/nginx/wordpress.itzgeek.local/logs/error.log;

location / {
        root /usr/share/nginx/wordpress.itzgeek.local;
        index index.php index.html index.htm;

if (-f $request_filename) {
        expires 30d;
        break;
}

if (!-e $request_filename) {
        rewrite ^(.+)$ /index.php?q=$1 last;
        }
}

location ~ .php$ {
        fastcgi_pass   localhost:9000;  # port where FastCGI processes were spawned
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME   /usr/share/nginx/wordpress.itzgeek.local$fastcgi_script_name;  # same path as above
        fastcgi_param PATH_INFO               $fastcgi_script_name;
        include /etc/nginx/fastcgi_params;
        }
}

mkdir /usr/share/nginx/wordpress.itzgeek.local
mkdir /usr/share/nginx/wordpress.itzgeek.local/logs
nginx -t
systemctl restart nginx
systemctl restart php-fpm
setenforce 0

(create database for wordpress)
mysql -u root -p
CREATE DATABASE wordpress;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'wppassword';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
exit;
wget http://wordpress.org/latest.tar.gz
tar -zxvf latest.tar.gz
mv wordpress/* /usr/share/nginx/wordpress.itzgeek.local
cp /usr/share/nginx/wordpress.itzgeek.local/wp-config-sample.php /usr/share/nginx/wordpress.itzgeek.local/wp-config.php
vi /usr/share/nginx/wordpress.itzgeek.local/wp-config.php

(set wordpress password and username database that we have set on mariadb)
chown -R nginx:nginx /usr/share/nginx/wordpress.itzgeek.local/
