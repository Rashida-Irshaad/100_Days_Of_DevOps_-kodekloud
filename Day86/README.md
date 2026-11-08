# Day 86 - Challenge 
# KodeKloud Task: Passwordless SSH + Ansible Ping to App Server 3

## Task Description

The Nautilus DevOps team is planning to test several Ansible playbooks on different app servers in `Stratos DC`. Before that, some pre-requisites must be met. Essentially, the team needs to set up a password-less SSH connection between the Ansible controller and Ansible managed nodes. One of the tickets is assigned to you; please complete the task as per details mentioned below:

a. `Jump host` is our Ansible controller, and we are going to run Ansible playbooks through the `thor` user from `jump host`.

b. There is an inventory file `/home/thor/ansible/inventory` on `jump host`. Using that inventory file, test `Ansible ping` from `jump host` to `App Server 3`, and make sure ping works.

---

## Server Details

| Server       | Hostname | User     |
|--------------|----------|----------|
| App Server 3 | `stapp03` | `banner` |

Password for `banner` is usually `BigGr33n` (confirm on your task's credentials page).

---

## Solution Steps

### Step 1: Confirm you are the `thor` user on the jump host
```bash
whoami
```
Expected output: `thor`

### Step 2: Look at the inventory file
```bash
cat /home/thor/ansible/inventory
```
Check the `stapp03` line. Note the host name, IP, and user, and look for any password entries (`ansible_ssh_pass` or `ansible_password`).

### Step 3: Generate an SSH key
```bash
ssh-keygen -t rsa
```
Press **Enter** at every prompt (default location, empty passphrase).

### Step 4: Copy the key to App Server 3
```bash
ssh-copy-id banner@stapp03
```
- Type `yes` if asked about the fingerprint.
- Enter the password for `banner`.

### Step 5: Test passwordless SSH
```bash
ssh banner@stapp03
```
It should log in without asking for a password. Then type `exit` to return.

### Step 6: Find the real IP of stapp03
```bash
getent hosts stapp03
```
Note the IP address that is printed.

### Step 7: Fix the inventory file
```bash
vi /home/thor/ansible/inventory
```
Make the `stapp03` line look like this (use the real IP from Step 6):
```
stapp03 ansible_host=<REAL_IP> ansible_user=banner
```
Remove any `ansible_ssh_pass` or `ansible_password` entries.

Save and exit vi: press `Esc`, type `:wq`, press `Enter`.

### Step 8: Run the Ansible ping
```bash
ansible -i /home/thor/ansible/inventory stapp03 -m ping
```

---

## Expected Result
```
stapp03 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

---

## Troubleshooting

| Problem | Fix |
|---|---|
| `Permission denied` | Re-run `ssh-copy-id banner@stapp03` with the correct password |
| `UNREACHABLE` / connection timed out | The `ansible_host` IP in the inventory is wrong; use the IP from `getent hosts stapp03` |
| Host not found | The name in the ansible command must match the inventory name exactly |
| Asks for a password | The key was not copied, or the inventory still contains a password line |

---

## Quick Copy-Paste Version
```bash
cat /home/thor/ansible/inventory
ssh-keygen -t rsa
ssh-copy-id banner@stapp03
getent hosts stapp03
vi /home/thor/ansible/inventory      # set: stapp03 ansible_host=<REAL_IP> ansible_user=banner
ansible -i /home/thor/ansible/inventory stapp03 -m ping
```