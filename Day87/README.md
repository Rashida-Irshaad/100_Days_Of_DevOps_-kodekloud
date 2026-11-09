# Day 87 - Challenge 
# KodeKloud Task: Install logrotate on App Servers Using Ansible

## Task Description

The Nautilus Application development team wanted to test some applications on app servers in `Stratos Datacenter`. They shared some pre-requisites with the DevOps team, and packages need to be installed on app servers. Since we are already using Ansible for automating such tasks, please perform this task using Ansible as per details mentioned below:

1. Create an inventory file `/home/thor/playbook/inventory` on `jump host` and add all app servers in it.
2. Create an Ansible playbook `/home/thor/playbook/playbook.yml` to install `logrotate` package on `all app servers` using Ansible `yum` module.
3. Make sure user `thor` should be able to run the playbook on `jump host`.

**Note:** Validation will try to run playbook using command `ansible-playbook -i inventory playbook.yml` so please make sure playbook works this way, without passing any extra arguments.

---

## Solution

### Step 1: Find the app server details

In the Stratos Datacenter, the app servers are normally:

| Server       | Hostname | User   | Password |
|--------------|----------|--------|----------|
| App Server 1 | stapp01  | tony   | Ir0nM@n  |
| App Server 2 | stapp02  | steve  | Am3ric@  |
| App Server 3 | stapp03  | banner | BigGr33n |

Check these against your task's "Details" or "Info" section, as they can differ.

### Step 2: Go to the playbook directory

On the jump host, as user `thor`:

```bash
cd /home/thor/playbook
```

If the folder doesn't exist:

```bash
mkdir -p /home/thor/playbook
cd /home/thor/playbook
```

### Step 3: Create the inventory file

```bash
vi inventory
```

Press `i`, paste the following, then press `Esc` and type `:wq` to save:

```ini
stapp01 ansible_host=stapp01 ansible_user=tony ansible_ssh_pass=Ir0nM@n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
stapp02 ansible_host=stapp02 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no'
stapp03 ansible_host=stapp03 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

Why: each server has its own user and password stored in the inventory, so the validation command needs no extra arguments. `StrictHostKeyChecking=no` prevents the yes/no host key prompt from blocking the run.

### Step 4: Create the playbook

```bash
vi playbook.yml
```

Paste and save:

```yaml
---
- name: Install logrotate on all app servers
  hosts: all
  become: yes
  tasks:
    - name: Install logrotate package
      yum:
        name: logrotate
        state: present
```

What it does:
- `hosts: all` runs on every server in the inventory.
- `become: yes` gives root rights, needed to install packages.
- `yum` is the module the task requires.
- `state: present` installs the package if it is missing.

### Step 5: Test connectivity (optional)

```bash
ansible -i inventory all -m ping
```

All three servers should reply with `pong`.

### Step 6: Run the playbook

Run it exactly as the validator will:

```bash
ansible-playbook -i inventory playbook.yml
```

Expected result:

```
stapp01 : ok=2  changed=1  unreachable=0  failed=0
stapp02 : ok=2  changed=1  unreachable=0  failed=0
stapp03 : ok=2  changed=1  unreachable=0  failed=0
```

`changed=1` means the package was installed. `changed=0` means it was already installed, which is also fine.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| `sshpass not found` | `sudo yum install sshpass -y` |
| Permission denied / authentication failed | Wrong password in inventory; re-check the task's info section |
| `sudo: a password is required` | Add `ansible_become_pass=<same password>` to that server's line |
| File permission error | `sudo chown -R thor:thor /home/thor/playbook` |
