# Day 6 - Challenge 
# Day 6 - Create Cron Job on All App Servers

## Task Description
The **Nautilus system admins team** wants to test a **sample cron job** on all App Servers within the **Stratos Datacenter**.  

Your task is to:

- Install the `cronie` package on all Nautilus App Servers.  
- Start and enable the `crond` service.  
- Add a cron job for the **root user** to run **every 5 minutes**, writing `"hello"` into `/tmp/cron_text`.  

---

## Steps to Complete Task

1. **Login to Each App Server**:
    ```bash
    ssh tony@stapp01   # App Server 1
    ssh steve@stapp02  # App Server 2
    ssh banner@stapp03 # App Server 3
    ```

2. **Install Cron Package**:
    ```bash
    sudo yum install -y cronie
    ```

3. **Start and Enable Cron Service**:
    ```bash
    sudo systemctl start crond
    sudo systemctl enable crond
    ```

4. **Add Cron Job for Root User**:
    ```bash
    sudo crontab -e
    ```
    - Add the following line inside the editor:
    ```cron
    */5 * * * * echo "hello" >> /tmp/cron_text
    ```
    - Save and exit (`Esc` → `:wq` → `Enter`).

5. **Verify Cron Job**:
    ```bash
    sudo crontab -l
    ```
    - Expected output:
    ```cron
    */5 * * * * echo "hello" >> /tmp/cron_text
    ```

6. **Optional: Test Cron Execution**:
    ```bash
    cat /tmp/cron_text
    ```
    - You should see repeated lines with `"hello"` written every 5 minutes.

---

## Done
- Cron job is now running on all App Servers and writing `"hello"` to `/tmp/cron_text` every 5 minutes.
