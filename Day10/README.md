# Day 10 - Challenge
# Day 10 - Create Website Backup Script on App Server 2

## Task Description
The **production support team** of xFusionCorp Industries is developing bash scripts to automate routine tasks.  

Your task is to create a **bash script** named `ecommerce_backup.sh` on **App Server 2** in the **Stratos Datacenter** to automate the backup of a static website.

The script should:

1. Create a zip archive of the `/var/www/html/ecommerce` directory named `xfusioncorp_ecommerce.zip`.  
2. Save the archive in `/backup/` on App Server 2.  
3. Copy the archive to the **Nautilus Backup Server** under `/backup/`.  
4. Ensure the script runs **without asking for passwords** when copying the archive.  
5. Make sure the respective server user (`steve` on App Server 2) can execute the script.  

---

## Steps to Complete Task

1. **Login to App Server 2**:
    ```bash
    ssh steve@stapp02
    ```

2. **Create the `/scripts` Directory** (if it doesn’t exist):
    ```bash
    sudo mkdir -p /scripts
    ```

3. **Create the Bash Script `ecommerce_backup.sh`**:
    ```bash
    sudo vi /scripts/ecommerce_backup.sh
    ```
    - Add the following content:
    ```bash
    #!/bin/bash
    # Define variables
    SRC_DIR="/var/www/html/ecommerce"
    BACKUP_DIR="/backup"
    BACKUP_FILE="xfusioncorp_ecommerce.zip"
    REMOTE_USER="backupuser"
    REMOTE_HOST="backupserver"
    REMOTE_DIR="/backup"

    # Create zip archive
    zip -r "$BACKUP_DIR/$BACKUP_FILE" "$SRC_DIR"

    # Copy backup to remote server without password prompt
    scp "$BACKUP_DIR/$BACKUP_FILE" "$REMOTE_USER@$REMOTE_HOST:$REMOTE_DIR"
    ```

4. **Make the Script Executable**:
    ```bash
    sudo chmod +x /scripts/ecommerce_backup.sh
    ```

5. **Ensure Password-less SSH from App Server 2 to Backup Server**:
    ```bash
    ssh-keygen -t rsa
    ssh-copy-id backupuser@backupserver
    ```

6. **Test the Script**:
    ```bash
    /scripts/ecommerce_backup.sh
    ```
    - Verify the backup exists locally:
    ```bash
    ls -l /backup/xfusioncorp_ecommerce.zip
    ```
    - Verify the backup exists on the backup server:
    ```bash
    ssh backupuser@backupserver "ls -l /backup/xfusioncorp_ecommerce.zip"
    ```

7. **Done**
- The script is now ready to automate website backups and copy them securely to the backup server without manual password input.

---

