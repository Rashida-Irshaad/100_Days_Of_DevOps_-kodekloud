## Task: Install and Configure Nginx with PHP-FPM

The Nautilus application development team wants to deploy a new PHP-based application on one of the app servers in the Stratos Datacenter. They require the following:

1. Install nginx on the given app server and configure it to use the specified port and document root.
2. Install and configure php-fpm to work with nginx.
3. Ensure the PHP info page is accessible via the browser using the server’s hostname and the given port.

---

### Solution Steps

1. SSH to the app server (e.g., stapp02):
   ssh steve@stapp02

2. Become root:
   sudo -i

3. Install nginx and php-fpm:
   dnf install -y nginx
   dnf module -y install php:8.1/common

4. Configure php-fpm:
   vi /etc/php-fpm.d/www.conf  
   Change:
   user = nginx  
   group = nginx  
   listen = /run/php-fpm/www.sock  

5. Set directory permissions:
   chown -R root:nginx /var/lib/php

6. Configure nginx:
   vi /etc/nginx/nginx.conf  
   Add or modify the server block:
   server {  
       listen       8094 default_server;  
       listen       [::]:8094 default_server;  
       server_name  _;  
       root         /var/www/html;  

       location / { }  

       location ~ \.php$ {  
           try_files $uri =404;  
           fastcgi_pass unix:/run/php-fpm/www.sock;  
           fastcgi_index index.php;  
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;  
           include fastcgi_params;  
       }  

       error_page 404 /404.html;  
           location = /40x.html { }  

       error_page 500 502 503 504 /50x.html;  
           location = /50x.html { }  
   }

7. Restart services:
   systemctl restart php-fpm  
   systemctl restart nginx

8. Test locally:
   curl http://stapp02:8094/index.php

9. Test from jump host:
   exit  
   curl http://stapp02:8094/index.php  

Expected Output:  
Welcome to xFusionCorp Industries!

