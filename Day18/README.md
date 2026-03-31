# Day 18 - Challenge
# Day 18 - Deploy WordPress Website on Nautilus Infra

## Task Description
xFusionCorp Industries is planning to host a **WordPress website** on their infrastructure in **Stratos Datacenter**.  

The storage server already has a shared directory `/vaw/www/html` mounted on each app host under `/var/www/html`.  

Your task is to:

- Install **httpd**, **PHP**, and dependencies on all app hosts.  
- Configure Apache to serve on **port 3000**.  
- Install and configure **MariaDB** on the database server (`stdb01`).  
- Create a database `kodekloud_db7`.  
- Create a database user `kodekloud_cap` with password `LQfKeWWxWD` and grant all privileges on `kodekloud_db7`.  
- Ensure the WordPress website can access the database using `kodekloud_cap`.  
- Verify access to the website through the **LBR** link by clicking the App button.

---

## Steps to Complete Task
```bash
# 1️⃣ Install Apache & PHP on all App Servers and configure port 3000
ssh tony@stapp01
sudo yum install -y httpd php php-mysqlnd php-cli php-gd
sudo sed -i 's/^Listen .*/Listen 3000/' /etc/httpd/conf/httpd.conf
sudo systemctl enable httpd
sudo systemctl start httpd
exit

ssh steve@stapp02
sudo yum install -y httpd php php-mysqlnd php-cli php-gd
sudo sed -i 's/^Listen .*/Listen 3000/' /etc/httpd/conf/httpd.conf
sudo systemctl enable httpd
sudo systemctl start httpd
exit

ssh banner@stapp03
sudo yum install -y httpd php php-mysqlnd php-cli php-gd
sudo sed -i 's/^Listen .*/Listen 3000/' /etc/httpd/conf/httpd.conf
sudo systemctl enable httpd
sudo systemctl start httpd
exit

# 2️⃣ Install and configure MariaDB on Database Server
ssh peter@stdb01
sudo yum install -y mariadb-server mariadb
sudo systemctl enable mariadb
sudo systemctl start mariadb

# 3️⃣ Create database and user with privileges
sudo mysql -u root -e "CREATE DATABASE kodekloud_db7;"
sudo mysql -u root -e "CREATE USER 'kodekloud_cap'@'%' IDENTIFIED BY 'LQfKeWWxWD';"
sudo mysql -u root -e "GRANT ALL PRIVILEGES ON kodekloud_db7.* TO 'kodekloud_cap'@'%';"
sudo mysql -u root -e "FLUSH PRIVILEGES;"

# 4️⃣ Verify database access
mysql -u kodekloud_cap -pLQfKeWWxWD -e "USE kodekloud_db7; SHOW TABLES;"

# 5️⃣ Restart Apache on all App Servers to ensure shared directory is accessible
ssh tony@stapp01
sudo systemctl restart httpd
exit

ssh steve@stapp02
sudo systemctl restart httpd
exit

ssh banner@stapp03
sudo systemctl restart httpd
exit

# 6️⃣ Test website via LBR
# Open the LBR link in a browser and click the App button.
# Confirm the application connects to the database using 'kodekloud_cap'.

