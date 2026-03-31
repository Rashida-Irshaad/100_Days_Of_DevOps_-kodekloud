# Day 16 - Challenge
# Day 16 - Configure Nginx Load Balancer on LBR Server

## Task Description
Due to increasing traffic on one of the websites, the **Nautilus production support team** decided to deploy the application on a **high availability stack** in Stratos DC. The migration is almost complete, and now the **LBR (Load Balancer) server)** needs to be configured.

Your task is to:

- Install **Nginx** on the LBR server.  
- Configure **load balancing** using all App Servers via HTTP context.  
- Ensure Apache services are running on all App Servers and their ports are not modified.  
- Use only the main Nginx configuration file located at `/etc/nginx/nginx.conf`.  
- Verify access to the website using the **StaticApp** button on the top bar.

---

## Steps to Complete Task

1. **Login to LBR Server**:
    ```bash
    ssh loki@LBR
    ```

2. **Install Nginx**:
    ```bash
    sudo yum install -y nginx
    ```

3. **Backup Original Nginx Configuration**:
    ```bash
    sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
    ```

4. **Edit Nginx Configuration to Enable Load Balancing**:
    ```bash
    sudo vi /etc/nginx/nginx.conf
    ```
    Add upstream block and modify server section:
    ```nginx
    http {
        upstream app_servers {
            server stapp01:3000;
            server stapp02:3000;
            server stapp03:3000;
        }

        server {
            listen 80;

            location / {
                proxy_pass http://app_servers;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            }
        }
    }
    ```

5. **Test Nginx Configuration**:
    ```bash
    sudo nginx -t
    ```

6. **Start and Enable Nginx**:
    ```bash
    sudo systemctl enable nginx
    sudo systemctl restart nginx
    sudo systemctl status nginx
    ```

7. **Verify Apache Services on App Servers**:
    ```bash
    ssh tony@stapp01 "sudo systemctl status httpd"
    ssh steve@stapp02 "sudo systemctl status httpd"
    ssh banner@stapp03 "sudo systemctl status httpd"
    ```

8. **Test Load Balancer Access**:
    - Open browser or use the **StaticApp** button on top bar to confirm the website is accessible via the LBR server.

---

**✅ Done**  
Nginx is installed and configured as a **load balancer** on the LBR server `loki@LBR`, distributing traffic to all App Servers while keeping their Apache services intact.
