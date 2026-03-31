# Day 19 - Challenge
# Day 19 - Host Two Static Websites on App Server 1

## Task Description
xFusionCorp Industries wants to host **two static websites** on **App Server 1** in the **Stratos Datacenter**.  

Requirements:  
- Install Apache (`httpd`) and dependencies on **App Server 1**.  
- Apache should listen on **port 3003**.  
- Two website backups are located at `/home/thor/ecommerce` and `/home/thor/demo` on `jump_host`.  
- Configure Apache so that:  
  - `http://localhost:3003/ecommerce/` serves the `ecommerce` site.  
  - `http://localhost:3003/demo/` serves the `demo` site.  
- Verify access using `curl` on **App Server 1**.  

---

## Steps to Complete Task
```bash
# 1️⃣ Login to App Server 1
ssh tony@stapp01

# 2️⃣ Install Apache and dependencies
sudo yum install -y httpd

# 3️⃣ Update Apache to listen on port 3003
sudo sed -i 's/^Listen .*/Listen 3003/' /etc/httpd/conf/httpd.conf

# 4️⃣ Copy website backups from jump_host to App Server 1
# On jump_host, copy ecommerce and demo folders
scp -r /home/thor/ecommerce tony@stapp01:/tmp/
scp -r /home/thor/demo tony@stapp01:/tmp/

# Back on App Server 1, move websites to Apache document root
sudo mkdir -p /var/www/html/ecommerce
sudo mkdir -p /var/www/html/demo
sudo cp -r /tmp/ecommerce/* /var/www/html/ecommerce/
sudo cp -r /tmp/demo/* /var/www/html/demo/

# 5️⃣ Create VirtualHost configuration for both sites
sudo bash -c 'cat <<EOF > /etc/httpd/conf.d/multi-sites.conf
<VirtualHost *:3003>
    DocumentRoot /var/www/html/ecommerce
    Alias /ecommerce /var/www/html/ecommerce
</VirtualHost>

<VirtualHost *:3003>
    DocumentRoot /var/www/html/demo
    Alias /demo /var/www/html/demo
</VirtualHost>
EOF'

# 6️⃣ Start and enable Apache service
sudo systemctl enable httpd
sudo systemctl restart httpd

# 7️⃣ Verify sites using curl
curl http://localhost:3003/ecommerce/
curl http://localhost:3003/demo/

