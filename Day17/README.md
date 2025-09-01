# Day 17 - Challenge
# Day 17 - Configure PostgreSQL Database on stdb01

## Task Description
The Nautilus application development team is preparing to deploy a new application that uses **PostgreSQL** as its backend database.  

Your task is to configure the **PostgreSQL database server** on **stdb01 (172.16.239.10)** according to the following requirements:

- Create a database user `kodekloud_sam` with password `Rc5C9EyvbU`.  
- Create a database `kodekloud_db3`.  
- Grant full privileges of `kodekloud_db3` to user `kodekloud_sam`.  
- **Do not** restart the PostgreSQL service.  

---

## Steps to Complete Task

1. **Login to the Database Server**:
    ```bash
    ssh peter@stdb01
    ```

2. **Switch to PostgreSQL User**:
    ```bash
    sudo -i -u postgres
    ```

3. **Create Database User**:
    ```bash
    psql -c "CREATE USER kodekloud_sam WITH PASSWORD 'Rc5C9EyvbU';"
    ```

4. **Create Database**:
    ```bash
    psql -c "CREATE DATABASE kodekloud_db3;"
    ```

5. **Grant Privileges to User on Database**:
    ```bash
    psql -c "GRANT ALL PRIVILEGES ON DATABASE kodekloud_db3 TO kodekloud_sam;"
    ```

6. **Verify User and Database**:
    ```bash
    psql -c "\du"
    psql -c "\l"
    ```

7. **Exit PostgreSQL User**:
    ```bash
    exit
    ```

---

**✅ Done**  
The PostgreSQL database `kodekloud_db3` is created on `stdb01` with full access granted to user `kodekloud_sam`. The PostgreSQL service remains running without a restart.
