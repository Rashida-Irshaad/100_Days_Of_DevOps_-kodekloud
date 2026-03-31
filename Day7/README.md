# Day 7 - Challenge
# Day 7 - Configure Password-less SSH Access for thor User

## Task Description
The **xFusionCorp Industries** system admins team has set up scripts on the **jump host** that run at regular intervals and perform operations on all App Servers within the **Stratos Datacenter**.  

To make these scripts work properly, the **thor** user on the jump host must have **password-less SSH access** to all App Servers through their respective sudo users.

---

## Steps to Complete Task

1. **Check if SSH Key Pair Already Exists**:
    ```bash
    ls -l ~/.ssh/id_rsa ~/.ssh/id_rsa.pub
    ```
    - If the files exist, you can skip key generation.  
    - If not, generate a new key pair.

2. **Generate SSH Key Pair** (if not already present):
    ```bash
    ssh-keygen -t rsa -b 2048
    ```
    - Press `Enter` to accept the default location (`~/.ssh/id_rsa`)  
    - Leave passphrase empty for password-less login

3. **Copy the Public Key to Each App Server**:
    - **App Server 1** (user `tony`):
        ```bash
        ssh-copy-id tony@stapp01
        ```
    - **App Server 2** (user `steve`):
        ```bash
        ssh-copy-id steve@stapp02
        ```
    - **App Server 3** (user `banner`):
        ```bash
        ssh-copy-id banner@stapp03
        ```

4. **Verify Password-less SSH Access**:
    ```bash
    ssh tony@stapp01
    ssh steve@stapp02
    ssh banner@stapp03
    ```
    - You should **not be prompted for a password**.

5. **Optional: Test Running Commands Remotely**:
    ```bash
    ssh tony@stapp01 "whoami"
    ssh steve@stapp02 "whoami"
    ssh banner@stapp03 "whoami"
    ```
    - Should return the respective user names (`tony`, `steve`, `banner`) without prompting for password.

6. **Done**
- Password-less SSH access is now configured for the **thor** user on the jump host to all App Servers.  
- This allows automated scripts on the jump host to run seamlessly on all App Servers.

---
