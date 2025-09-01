# Day 14 - Challenge
# Day 14 - Ensure Apache is Running on Port 3000 on All App Servers

## Task Description
The **production support team** reported that the **Apache service** is unavailable on one of the app servers in the **Stratos Datacenter**.  

Your task is to:

- Identify the faulty app server where Apache is down.  
- Ensure that **Apache is installed, running, and enabled** on all app servers.  
- Configure Apache to run on port **3000** on all app servers.  
- Verify the service is up, even if no website content is hosted yet.  

---

## Steps to Complete Task

1. **Login to each App Server**:
    ```bash
    ssh tony@stapp01
    ssh tony@stapp02
    ssh tony@stapp03
    ```

2. **Install Apache if not already installed**:
    ```bash
    sudo yum install -y httpd
    ```

3. **Edit Apache configuration to listen on port 3000**:
    ```bash
    sudo sed -i 's/^Listen.*/Listen 3000/' /etc/httpd/conf/httpd.conf
    ```

4. **Restart and enable Apache service**:
    ```bash
    sudo systemctl enable httpd
    sudo systemctl restart httpd
    sudo systemctl status httpd
    ```

5. **Verify Apache is listening on port 3000**:
    ```bash
    sudo netstat -tulnp | grep 3000
    ```

6. **Optional: Test locally**:
    ```bash
    curl http://localhost:3000
    ```

7. **Repeat for all app servers** to ensure uniform configuration.

---

**✅ Done**  
Apache is installed, running, and configured to listen on port **3000** on all app servers. The service is up and enabled on system boot.

