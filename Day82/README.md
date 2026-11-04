# Day 82 - Challenge 
# KodeKloud Task: Create Ansible Inventory for App Server 3

## Task Description
The Nautilus DevOps team is testing Ansible playbooks on various servers within their stack. They've placed some playbooks under `/home/thor/playbook/` on the `jump host` and now intend to test them on `app server 3` in `Stratos DC`. An inventory file needs to be created for Ansible to connect to the app server.

Requirements:
a. Create an ini type Ansible inventory file `/home/thor/playbook/inventory` on the jump host.
b. Include `App Server 3` in this inventory along with the necessary variables for proper functionality.
c. Ensure the inventory hostname matches the server name as per the wiki (for example, `stapp01` for app server 1 in Stratos DC).

Note: Validation will run `ansible-playbook -i inventory playbook.yml`. The playbook must work without any extra arguments.

---

## Solution Steps

### Step 1: Confirm you are on the jump host as thor
```bash
whoami
```
Expected output: `thor`

### Step 2: Go to the playbook directory
```bash
cd /home/thor/playbook/
ls
```
Expected files: `ansible.cfg  playbook.yml`

### Step 3: Find App Server 3 details
Server name in Stratos DC: `stapp03`

Check that the name resolves and note the IP:
```bash
getent hosts stapp03
```
In this lab it returned `10.244.164.61`. The IP can differ between lab sessions, so using the name `stapp03` is safest.

Credentials for App Server 3 (confirm on the Stratos DC wiki page):
- User: `banner`
- Password: `BigGr33n`

### Step 4: Create the inventory file
```bash
vi inventory
```
Press `i` to enter insert mode and paste this single line:

```ini
stapp03 ansible_host=stapp03 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

Save and exit: press `Esc`, type `:wq`, press `Enter`.

(If you previously created the file with a wrong IP, first delete the old line with `dd` in vi, then paste the line above.)

### Step 5: Verify the file
```bash
cat inventory
```
It should show exactly one line.

### Step 6: Test the connection
```bash
ansible -i inventory stapp03 -m ping
```
Expected: `"ping": "pong"`

### Step 7: Run the playbook the same way validation will
```bash
ansible-playbook -i inventory playbook.yml
```
Expected: the recap ends with `failed=0` and `unreachable=0`.

---

## What each variable means
| Variable | Meaning |
|---|---|
| `stapp03` | Inventory hostname (must match the wiki server name) |
| `ansible_host` | Address Ansible connects to (name or IP) |
| `ansible_user` | SSH login user |
| `ansible_ssh_pass` | SSH password |
| `ansible_ssh_common_args='-o StrictHostKeyChecking=no'` | Skips the yes/no host key prompt so the playbook runs without extra arguments |

---

## Troubleshooting
| Problem | Fix |
|---|---|
| Connection timed out | Wrong IP. Use `ansible_host=stapp03` or the IP from `getent hosts stapp03` |
| Permission denied | Wrong user/password. Check the wiki |
| sshpass not installed | `sudo yum install -y sshpass` |
| "No hosts matched" | Check `hosts:` in `playbook.yml`. It must be `stapp03` or `all` |
| Playbook needs root | Append `ansible_become=true ansible_become_pass=BigGr33n` to the inventory line |