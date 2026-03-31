
# Day 13 - Challenge
# Day 13 - Secure Apache Port 8088 on All App Servers

## Task Description
Your security team has raised a concern that **Apache’s port 8088** is currently open to all on all app servers in the **Stratos Datacenter**, and there is no firewall configured.  

Your task is to:

- Install **iptables** and all its dependencies on each app host.  
- Block incoming traffic on port **8088** for everyone **except the LBR host**.  
- Ensure that these rules persist after a system reboot.

---

## Steps to Complete Task

1. **Login to each App Server**:
    ```bash
    ssh tony@stapp01
    ssh tony@stapp02
    ssh tony@stapp03
    ```

2. **Install iptables and dependencies**:
    ```bash
    sudo yum install -y iptables-services iptables
    ```

3. **Flush existing rules (optional, for clean setup)**:
    ```bash
    sudo iptables -F
    ```

4. **Allow LBR host IP and block all others on port 8088**:
    ```bash
    # Replace <LBR_IP> with actual LBR host IP
    sudo iptables -A INPUT -p tcp --dport 8088 -s <LBR_IP> -j ACCEPT
    sudo iptables -A INPUT -p tcp --dport 8088 -j DROP
    ```

5. **Verify iptables rules**:
    ```bash
    sudo iptables -L -n --line-numbers
    ```

6. **Save iptables rules to persist after reboot**:
    ```bash
    sudo service iptables save
    sudo systemctl enable iptables
    sudo systemctl restart iptables
    ```

7. **Test access from allowed and blocked hosts**:
    ```bash
    # From LBR host
    curl http://stapp01:8088
    curl http://stapp02:8088
    curl http://stapp03:8088

    # From a different host (should be blocked)
    curl http://stapp01:8088
    ```

---

**✅ Done**  
Port **8088** is now restricted to the **LBR host only**, iptables is installed on all app servers, and rules persist across reboots.

