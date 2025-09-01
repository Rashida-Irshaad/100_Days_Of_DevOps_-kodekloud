# Day 1 - Challenge 

# Day 1 - Linux User Setup with Non-Interactive Shell

## Task
To accommodate the backup agent tool's specifications, the system admin team at **xFusionCorp Industries** requires the creation of a user with a non-interactive shell.

**Goal:**  
Create a user named `jim` with a non-interactive shell on App Server 2.

---

## Steps / Solution

1.SSH to App Server 2 

ssh steve@stapp02

2.Create the user with a non-interactive shell

sudo useradd -s /sbin/nologin jim

3.Verify the user is created

getent passwd jim


4.Exit the server

exit