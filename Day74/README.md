# Day 74 - Challenge 
# Day 74 - Jenkins: Automate Database Backup (KodeKloud)

## Task Description

The Nautilus DevOps team wanted to automate database backups using Jenkins.

The objective was to create a Jenkins job that connects to **App Server 1**, takes a backup of the **kodekloud_db01** database, copies the backup to the **Storage Server**, and schedules the job to run automatically every 10 minutes.

### Requirements

- Create a Jenkins job named **database-backup**.
- Take a database dump of:
  - **Database:** `kodekloud_db01`
  - **Username:** `kodekloud_roy`
  - **Password:** `asdfgdsd`
- Name the dump file as:
  ```
  db_$(date +%F).sql
  ```
- Copy the backup file to:
  ```
  /home/natasha/db_backups
  ```
  on the Storage Server (`ststor01`).
- Schedule the Jenkins job to run every 10 minutes using:
  ```
  */10 * * * *
  ```
- Build the job successfully at least once.

---

# Solution

## Step 1: Generate SSH Key on Jenkins Server

Login to the Jenkins server as the `jenkins` user.

Generate an SSH key pair:

```bash
ssh-keygen -t rsa -b 4096 -N "" -f ~/.ssh/id_rsa
```

Verify:

```bash
ls ~/.ssh
```

Expected output:

```text
id_rsa
id_rsa.pub
known_hosts
```

Display the public key:

```bash
cat ~/.ssh/id_rsa.pub
```

Copy the complete public key.

---

## Step 2: Configure Passwordless SSH

### On App Server (stapp01)

Login:

```bash
ssh tony@stapp01
```

Create SSH directory:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

Open the authorized_keys file:

```bash
vi ~/.ssh/authorized_keys
```

Paste the Jenkins public key.

Save and exit:

```
:wq
```

Set permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
```

Exit:

```bash
exit
```

---

### On Storage Server (ststor01)

Login:

```bash
ssh natasha@ststor01
```

Create SSH directory:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

Open:

```bash
vi ~/.ssh/authorized_keys
```

Paste the same Jenkins public key.

Save:

```
:wq
```

Set permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
```

Ensure backup directory exists:

```bash
mkdir -p /home/natasha/db_backups
```

Exit:

```bash
exit
```

---

## Step 3: Verify Passwordless SSH

From the Jenkins server:

```bash
ssh tony@stapp01 hostname
```

Expected:

```text
stapp01
```

Verify Storage Server:

```bash
ssh natasha@ststor01 hostname
```

Expected:

```text
ststor01
```

No password should be requested.

---

# Jenkins Configuration

## Step 4: Login

Open Jenkins.

Login using:

```
Username: admin
Password: Adm!n321
```

---

## Step 5: Create Job

Click

```
New Item
```

Job Name:

```
database-backup
```

Select:

```
Freestyle Project
```

Click **OK**.

---

## Step 6: Configure Build Trigger

Enable:

```
Build periodically
```

Cron expression:

```
*/10 * * * *
```

---

## Step 7: Configure Build Step

Click:

```
Add Build Step
```

Select:

```
Execute Shell
```

Paste:

```bash
ssh -o StrictHostKeyChecking=no tony@stapp01 "
mysqldump -u kodekloud_roy -pasdfgdsd kodekloud_db01 > /tmp/db_\$(date +%F).sql &&
scp /tmp/db_\$(date +%F).sql natasha@ststor01:/home/natasha/db_backups/
"
```

Save the job.

---

## Step 8: Build the Job

Click:

```
Build Now
```

The build should complete successfully.

---

## Step 9: Verify Backup

Login to the Storage Server:

```bash
ssh natasha@ststor01
```

Check the backup:

```bash
ls -l /home/natasha/db_backups
```

Expected output:

```text
db_2026-07-06.sql
```

(or the current date)

---

# Troubleshooting

### Problem

The build initially failed with:

```text
Permission denied (publickey,password)
```

### Cause

Passwordless SSH was not configured between the Jenkins server and the target servers.

### Solution

Generated an SSH key pair on the Jenkins server and copied the Jenkins public key into:

```
~/.ssh/authorized_keys
```

on both:

- App Server (`stapp01`)
- Storage Server (`ststor01`)

After configuring passwordless SSH, the Jenkins job completed successfully.

---

# Verification

The following checks confirmed the task was completed successfully:

- Jenkins job created successfully.
- Database dump generated.
- Backup file named using the current date.
- Backup copied to the Storage Server.
- Cron schedule configured as:
  ```
  */10 * * * *
  ```
- Build executed successfully.
- Backup file verified under:
  ```
  /home/natasha/db_backups
  ```

---

# Result

Successfully automated the backup of the `kodekloud_db01` MySQL database using Jenkins. The job creates a date-based SQL dump, securely transfers it to the Storage Server, and is scheduled to execute automatically every 10 minutes.
