# Day 9 - Challenge 
# Day 9 - Troubleshoot and Fix MariaDB Service on Database Server

## Task Description
A critical issue has been reported with the **Nautilus application** in the **Stratos Datacenter**.  

The production support team found that the application is unable to connect to the database because the **MariaDB service is down** on the database server.  

Your task is to:

- Investigate the issue on the database server.  
- Start and enable the MariaDB service to restore application connectivity.

---

## Steps to Complete Task

1. **Login to Database Server**:
    ```bash
    ssh dbadmin@dbserver
    ```

2. **Check Status of MariaDB Service**:
    ```bash
    sudo systemctl status mariadb
    ```
    - Look for whether the service is inactive, failed, or stopped.

3. **Start MariaDB Service**:
    ```bash
    sudo systemctl start mariadb
    ```

4. **Enable MariaDB to Start on Boot**:
    ```bash
    sudo systemctl enable mariadb
    ```

5. **Verify MariaDB is Running**:
    ```bash
    sudo systemctl status mariadb
    ```
    - Expected output: `active (running)`

6. **Test Database Connectivity** (Optional):
    ```bash
    mysql -u root -p -e "SHOW DATABASES;"
    ```
    - Ensure the MariaDB server responds correctly.

7. **Done**
- The MariaDB service is now running and the Nautilus application should be able to connect to the database.

---

