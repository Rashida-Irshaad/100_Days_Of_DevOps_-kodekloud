# Day 5 - Challenge 
# Day 5 - Challenge
# Day 5 - Install and Disable SELinux on App Server 3

## Task Description
Following a security audit, the **xFusionCorp Industries** security team has decided to begin **SELinux testing**.  
On App Server 3 within the **Stratos Datacenter**, your task is to:

- Install the required SELinux packages.  
- Permanently disable SELinux (it will be re-enabled later).  
- Do **not** reboot the server now; a maintenance reboot is planned tonight.  
- Ignore the current `sestatus` output — the permanent setting after reboot must be disabled.

---

## Requirements
- **Server**: App Server 3 (`stapp03`)  
- **User**: `banner`  
- **Packages**: `selinux-policy`, `selinux-policy-targeted`, `policycoreutils`  
- **SELinux Setting**: Permanent disable (`SELINUX=disabled`)  

---

## Steps to Complete Task
1. **Login to App Server 3**:
    ```bash
    ssh banner@stapp03
    ```

2. **Install SELinux Packages**:
    ```bash
    sudo yum install -y selinux-policy selinux-policy-targeted policycoreutils
    ```

3. **Edit the SELinux Configuration File**:
    ```bash
    sudo vi /etc/selinux/config
    ```
    - Find the line:
    ```
    SELINUX=enforcing
    ```
    - Change it to:
    ```
    SELINUX=disabled
    ```
    - Save and exit:
        - Press `Esc`  
        - Type `:wq`  
        - Hit `Enter`

4. **Verify the Configuration File Change**:
    ```bash
    grep ^SELINUX= /etc/selinux/config
    ```
    Expected output:
    ```
    SELINUX=disabled
    ```

5. **Done**
- SELinux will be permanently disabled after the planned maintenance reboot tonight.

---

