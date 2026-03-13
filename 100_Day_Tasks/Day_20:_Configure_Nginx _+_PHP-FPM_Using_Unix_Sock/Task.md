### Task :

The Nautilus application development team is planning to launch a new PHP-based application, which they want to deploy on Nautilus infra in Stratos DC. The development team had a meeting with the production support team and they have shared some requirements regarding the infrastructure. Below are the requirements they shared:

a. Install nginx on app server 3 , configure it to use port 8096 and its document root should be /var/www/html.

b. Install php-fpm version 8.2 on app server 3, it must use the unix socket /var/run/php-fpm/default.sock (create the parent directories if don't exist).

c. Configure php-fpm and nginx to work together.

d. Once configured correctly, you can test the website using curl http://stapp03:8093/index.php command from jump host.



### This is what i did to accomplish the task:

### Login to app server 3

```
ssh banner@stapp03
```

### Install Nginx

```
sudo yum install -y nginx
sudo systemctl enable nginx
```
### Configure NGINX for the app

Edited /etc/nginx/nginx.conf:

sudo vi /etc/nginx/nginx.conf

```
 server {
        listen       8096 ;
        listen       [::]:8096;
        server_name  stapp03;
        root /var/www/html;
 index index.php index.html index.htm;

 location / {
 try_files $uri $uri/ =404;
 }

 location ~\.php$ {
    include fastcgi_params;
    fastcgi_pass unix:/var/run/php-fpm/default.sock;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
}

  error_page 404 /404.html;
  error_page 500 502 503 504 /50x.html;

```



sudo systemctl restart nginx

### Install PHP-FPM 8.2

```
 sudo dnf install epel-release -y
 sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-9.rpm -y
 sudo dnf module list php
 sudo dnf module enable php:remi-8.2 -y
 sudo dnf install php-fpm php php-cli php-common php-mysqlnd php-gd php-xml php-mbstring php-pdo php-opcache -y
 sudo systemctl start php-fpm
 sudo systemctl enable php-fpm
 
```
php -v
sudo systemctl status php-fpm

### Configure PHP-FPM
Edited /etc/php-fpm.d/www.conf:

sudo vi /etc/php-fpm.d/www.conf

``` 
user = nginx

group = nginx

listen = /var/run/php-fpm/default.sock

listen.owner = nginx

listen.group = nginx

listen.mode = 0660

```

### Also, ensure the directory exists:

sudo mkdir -p /var/run/php-fpm

sudo chown -R nginx:nginx /var/run/php-fpm

sudo systemctl restart php-fpm

sudo systemctl enable --now php-fpm

sudo systemctl restart nginx # or apache2, etc.

curl localhost:8096


#### Testing:

From the Jump Host, I ran: 

Curl.. command
