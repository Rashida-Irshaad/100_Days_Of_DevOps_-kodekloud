# Day 12 - Challenge 
# Day 12 - Make Apache Listen on Port 6000 on App Server 1

## Task Description
Your monitoring tool reported that **Apache on stapp01** is not reachable on port **6000**.  

Possible reasons:
1. Apache service is down.
2. Port 6000 is blocked or already in use.
3. Firewall is blocking external access.

Your task is to:

- Make Apache listen on port **6000**.  
- Ensure no other service (like sendmail) is blocking the port.  
- Open the firewall so Apache is reachable from the jump host.  
- Test using `curl http://stapp01:6000`.

---

## Steps to Complete Task

1. **Login to App Server 1**:
    ```bash
    ssh tony@stapp01
    ```

2. **Stop services using port 6000 (sendmail)**:
    ```bash
    sudo systemctl stop sendmail
    sudo systemctl disable sendmail
    ```

3. **Configure Apache to listen on port 6000**:
    ```bash
    sudo sed -i 's/^#Listen.*/Listen 6000/' /etc/httpd/conf/httpd.conf
    sudo sed -i '/^Listen 80/d' /etc/httpd/conf/httpd.conf
    ```

4. **Test Apache configuration**:
    ```bash
    sudo httpd -t
    ```

5. **Restart Apache**:
    ```bash
    sudo systemctl restart httpd
    sudo systemctl status httpd
    ```

6. **Allow port 6000 through iptables**:
    ```bash
    sudo iptables -I INPUT 4 -p tcp --dport 6000 -j ACCEPT
    ```

7. **Save iptables rules to persist after reboot**:
    ```bash
    sudo iptables-save | sudo tee /etc/sysconfig/iptables
    ```

8. **Verify Apache is listening on port 6000**:
    ```bash
    sudo netstat -tulnp | grep 6000
    ```

9. **Test Apache locally**:
    ```bash
    curl http://localhost:6000
    ```

10. **Test Apache from the jump host**:
    ```bash
    curl http://stapp01:6000
    ```

---

**✅ Done**  
Apache is now configured to listen on port **6000**, firewall rules are updated, and the service is reachable from the jump host.

