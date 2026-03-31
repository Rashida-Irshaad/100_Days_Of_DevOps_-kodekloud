# Day 3 - Challenge 

## - Disable Direct Root SSH Login on All App Servers

### Task Description
Following a recent security audit, the **xFusionCorp Industries** security team has introduced stricter policies.  
Your task is to **disable direct SSH root login** on all App Servers within the **Stratos Datacenter**, so that users log in with their own accounts and use **sudo** for administrative tasks.

---

### Requirements
- **Action**: Disable direct root SSH login  
- **Servers**:  
  - App Server 1 (`stapp01`) → User: `tony`  
  - App Server 2 (`stapp02`) → User: `steve`  
  - App Server 3 (`stapp03`) → User: `banner`  
- **SSH Config File**: `/etc/ssh/sshd_config`  
- **Directive**: `PermitRootLogin no`  

---

### Steps to Complete Task
1. **Login to App Server 1**:
    ```bash
    ssh tony@stapp01
    ```

2. **Login to App Server 2**:
    ```bash
    ssh steve@stapp02
    ```

3. **Login to App Server 3**:
    ```bash
    ssh banner@stapp03
    ```

4. **Switch to Root User**:
    ```bash
    sudo su -
    ```

5. **Edit SSH Configuration File**:
    ```bash
    vi /etc/ssh/sshd_config
    ```
    - Find the line:
      ```
      #PermitRootLogin yes
      ```
    - Change it to:
      ```
      PermitRootLogin no
      ```
    - If the line doesn’t exist, add it manually.

6. **Save and Exit**:
    - Press `Esc`  
    - Type `:wq`  
    - Hit `Enter`

7. **Restart SSH Service**:
    ```bash
    systemctl restart sshd
    ```

8. **Verify SSH Configuration**:
    ```bash
    grep -i 'permitrootlogin' /etc/ssh/sshd_config
    ```
    Expected output:
    ```
    PermitRootLogin no
    ```
