## Task: Install and Configure Nginx with PHP-FPM

## Configure Nginx + PHP-FPM Using Unix Sock

The Nautilus application development team is planning to launch a new PHP-based application, which they want to deploy on Nautilus infra in Stratos DC. The development team had a meeting with the production support team and shared these requirements:

1. Install nginx on app server 2, configure it to use port 8094 and its document root should be `/var/www/html`.
2. Install php-fpm version 8.1 on app server 2, it must use the unix socket `/var/run/php-fpm/default.sock` (create parent directories if they don't exist).
3. Configure php-fpm and nginx to work together.
4. After configuration, test using:
   curl http://stapp02:8094/index.php

---

Solution Steps:

ssh steve@stapp02
sudo -i
dnf install -y nginx
dnf module -y install php:8.1/common

vi /etc/php-fpm.d/www.conf
# Change:
# user = nginx
# group = nginx
# listen = /var/run/php-fpm/default.sock
# Save and exit

chown -R root:nginx /var/lib/php

vi /etc/nginx/nginx.conf
# Update server block:
server {
    listen       8094 default_server;
    listen       [::]:8094 default_server;
    server_name  _;
    root         /var/www/html;

    include /etc/nginx/default.d/*.conf;

    location / {
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass php-fpm;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    error_page 404 /404.html;
        location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    }
}

vi /etc/nginx/conf.d/php-fpm.conf
# Add:
upstream php-fpm {
    server unix:/var/run/php-fpm/default.sock;
}

systemctl restart php-fpm
systemctl restart nginx

curl http://stapp02:8094/index.php
exit


9. Test from jump host:
     
   curl http://stapp02:8094/index.php  

Expected Output:  
Welcome to xFusionCorp Industries!

