
# Day 8 - Challenge
# Day 8 - Install Ansible 4.10.0 on Jump Host

## Task Description
During the weekly meeting, the **Nautilus DevOps team** decided to start testing **Ansible** for automation and configuration management tasks.  

Your task is to:

- Install **Ansible version 4.10.0** on the **jump host** using **pip3** only.  
- Ensure the **Ansible binary is available globally**, so all users on the system can run Ansible commands.

---

## Steps to Complete Task

1. **Login to Jump Host**:
    ```bash
    ssh thor@jump_host
    ```

2. **Update System Packages** (optional but recommended):
    ```bash
    sudo dnf update -y      # For RHEL/CentOS
    sudo apt update -y      # For Ubuntu/Debian
    ```

3. **Install pip3** (if not already installed):
    ```bash
    sudo dnf install -y python3-pip   # RHEL/CentOS
    sudo apt install -y python3-pip   # Ubuntu/Debian
    ```

4. **Install Ansible 4.10.0 Using pip3**:
    ```bash
    sudo pip3 install ansible==4.10.0
    ```

5. **Verify Installation**:
    ```bash
    ansible --version
    ```
    - Expected output should show **ansible 4.10.0** and the Python version being used.

6. **Ensure Global Availability**:
    ```bash
    which ansible
    ```
    - Should return a path like `/usr/local/bin/ansible`  
    - All users on the system should now be able to run `ansible` commands.

7. **Done**
- Ansible 4.10.0 is now installed on the jump host and globally accessible for all users.

---

