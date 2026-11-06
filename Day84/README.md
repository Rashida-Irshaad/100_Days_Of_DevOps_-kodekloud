# Day 84 - Challenge 
# KodeKloud Task: Copy Data to App Servers Using Ansible

## Task Description

The Nautilus DevOps team needs to copy data from the `jump host` to all `application servers` in `Stratos DC` using Ansible. Execute the task with the following details:

a. Create an inventory file `/home/thor/ansible/inventory` on `jump_host` and add all application servers as managed nodes.

b. Create a playbook `/home/thor/ansible/playbook.yml` on the `jump host` to copy the `/usr/src/dba/index.html` file to all application servers, placing it at `/opt/dba`.

**Note:** Validation will run the playbook using the command `ansible-playbook -i inventory playbook.yml`. Ensure the playbook functions properly without any extra arguments.

---

## Server Details (Stratos DC)

Verify these in your lab, as they can differ.

| Server  | User   | Password |
|---------|--------|----------|
| stapp01 | tony   | Ir0nM@n  |
| stapp02 | steve  | Am3ric@  |
| stapp03 | banner | BigGr33n |

---

## Solution

### Step 1: Go to the ansible directory

```bash
mkdir -p /home/thor/ansible
cd /home/thor/ansible
```

### Step 2: Create the inventory file

```bash
vi /home/thor/ansible/inventory
```

Paste this, then save with `Esc`, `:wq`, `Enter`:

```ini
stapp01 ansible_user=tony ansible_ssh_pass=Ir0nM@n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
stapp02 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no'
stapp03 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

What it does:
- Each line is one managed node (app server).
- `ansible_user` is the login user.
- `ansible_ssh_pass` is the password, so no extra arguments are needed.
- `StrictHostKeyChecking=no` skips the host key confirmation prompt.

### Step 3: Create the playbook

```bash
vi /home/thor/ansible/playbook.yml
```

Paste this, then save with `Esc`, `:wq`, `Enter`:

```yaml
---
- name: Copy index.html to all app servers
  hosts: all
  become: yes
  tasks:
    - name: Copy index.html to /opt/dba
      copy:
        src: /usr/src/dba/index.html
        dest: /opt/dba/index.html
```

What it does:
- `hosts: all` runs on every server in the inventory.
- `become: yes` uses sudo for permissions on `/opt/dba`.
- `copy` sends the file from the jump host to the app servers.

### Step 4: Run the playbook

```bash
cd /home/thor/ansible
ansible-playbook -i inventory playbook.yml
```

Expected result:

```
stapp01 : ok=2 changed=1 failed=0
stapp02 : ok=2 changed=1 failed=0
stapp03 : ok=2 changed=1 failed=0
```

---

## Troubleshooting

**1. `/opt/dba` does not exist on the servers.** Use this playbook instead:

```yaml
---
- name: Copy index.html to all app servers
  hosts: all
  become: yes
  tasks:
    - name: Ensure /opt/dba exists
      file:
        path: /opt/dba
        state: directory
        mode: '0755'

    - name: Copy index.html to /opt/dba
      copy:
        src: /usr/src/dba/index.html
        dest: /opt/dba/index.html
```

**2. "Missing sudo password" error.** Add `ansible_become_pass=<same password>` to each inventory line, for example:

```ini
stapp01 ansible_user=tony ansible_ssh_pass=Ir0nM@n ansible_become_pass=Ir0nM@n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

**3. "sshpass not found" error.**

```bash
sudo yum install -y sshpass
```

**4. Verify the file arrived (optional):**

```bash
ssh tony@stapp01 "ls -l /opt/dba"
```

---

## Summary

1. Create `/home/thor/ansible/inventory` with all 3 app servers.
2. Create `/home/thor/ansible/playbook.yml` with the `copy` task.
3. Run `ansible-playbook -i inventory playbook.yml`.