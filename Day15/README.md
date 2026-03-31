# Day 15 - Challenge
# Day 15 - Deploy Nginx with SSL on App Server 1

## Task Description
The **system admins team** needs to prepare **App Server 1** in the **Stratos Datacenter** for a new application deployment.  

Your task is to:

- Install and configure **Nginx**.  
- Deploy a **self-signed SSL certificate** and key located at `/tmp/nautilus.crt` and `/tmp/nautilus.key`.  
- Move the certificate and key to an appropriate location and configure Nginx to use them.  
- Create an `index.html` file under the Nginx document root with the content **Welcome!**.  
- Test HTTPS access from the **Jump host** using `curl`.

---

## Steps to Complete Task

1. **Login to App Server 1**:
    ```bash
    ssh tony@stapp01
    ```

2. **Install Nginx**:
    ```bash
    sudo yum install -y nginx
    ```

3. **Move SSL certificate and key to /etc/ssl/**:
    ```bash
    sudo mkdir -p /etc/ssl/nautilus
    sudo mv /tmp/nautilus.crt /etc/ssl/nautilus/
    sudo mv /tmp/nautilus.key /etc/ssl/nautilus/
    sudo chmod 600 /etc/ssl/nautilus/*
    ```

4. **Configure Nginx for SSL**:
    ```bash
    sudo vi /etc/nginx/conf.d/nautilus.conf
    ```
    Add the following:
    ```nginx
    server {
        listen 443 ssl;
        server_name stapp01;

        ssl_certificate /etc/ssl/nautilus/nautilus.crt;
        ssl_certificate_key /etc/ssl/nautilus/nautilus.key;

        root /usr/share/nginx/html;
        index index.html;

        location / {
            try_files $uri $uri/ =404;
        }
    }
    ```

5. **Create index.html with content "Welcome!"**:
    ```bash
    echo "Welcome!" | sudo tee /usr/share/nginx/html/index.html
    ```

6. **Start and enable Nginx service**:
    ```bash
    sudo systemctl enable nginx
    sudo systemctl restart nginx
    sudo systemctl status nginx
    ```

7. **Test HTTPS access from Jump Host**:
    ```bash
    curl -Ik https://stapp01/
    ```
    You should see a `HTTP/1.1 200 OK` response with the SSL certificate in use.

---

**✅ Done**  
Nginx is installed and configured with SSL on **App Server 1**, and the `index.html` page is accessible over HTTPS.

