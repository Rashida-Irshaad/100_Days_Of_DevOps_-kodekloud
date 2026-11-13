# Day 91 - Challenge
# KodeKloud Task: Install and Configure httpd Using Ansible

## Task Description

The Nautilus DevOps team wants to install and set up a simple `httpd` web server on all app servers in `Stratos DC`. They also want to deploy a sample web page using Ansible. Write the required playbook as per the details below.

An `inventory` file already exists under `/home/thor/ansible` on the `jump host`. Write a playbook `playbook.yml` under `/home/thor/ansible` on the `jump host` itself. Using the playbook, perform these tasks:

1. Install `httpd` web server on all app servers, and make sure its service is up and running.
2. Create a file `/var/www/html/index.html` with content:
   `This is a Nautilus sample file, created using Ansible!`
3. Using the `lineinfile` Ansible module, add more content to `/var/www/html/index.html`:
   `Welcome to Nautilus Group!`
   Make sure this new line is added at the **top** of the file.
4. The `/var/www/html/index.html` file's user and group owner should be `apache` on all app servers.
5. The `/var/www/html/index.html` file's permissions should be `0655` on all app servers.

**Note:** Validation will run the playbook using `ansible-playbook -i inventory playbook.yml`, so make sure it works this way without any extra arguments.

---

## Solution

### Step 1: Go to the ansible directory

```bash
cd /home/thor/ansible
```

### Step 2: Check the inventory file

```bash
cat inventory
```

Check the host names/group name. Using `hosts: all` in the playbook targets every server in the inventory, which is the safest option.

### Step 3: Create the playbook

```bash
vi playbook.yml
```

Press `i` to enter insert mode, then paste:

```yaml
---
- name: Install and configure httpd on app servers
  hosts: all
  become: yes
  tasks:

    - name: Install httpd
      yum:
        name: httpd
        state: present

    - name: Start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Create index.html with initial content
      copy:
        content: "This is a Nautilus sample file, created using Ansible!"
        dest: /var/www/html/index.html

    - name: Add welcome line at the top using lineinfile
      lineinfile:
        path: /var/www/html/index.html
        line: "Welcome to Nautilus Group!"
        insertbefore: BOF

    - name: Set owner, group and permissions
      file:
        path: /var/www/html/index.html
        owner: apache
        group: apache
        mode: '0655'
```

Save and exit: press `Esc`, type `:wq`, press `Enter`.

### Step 4: Run the playbook (same as the validator)

```bash
ansible-playbook -i inventory playbook.yml
```

The final recap should show `failed=0` for all hosts.

### Step 5: Verify (optional)

```bash
ansible all -i inventory -m shell -a "cat /var/www/html/index.html; ls -l /var/www/html/index.html" -b
```

Expected output on each server:

```
Welcome to Nautilus Group!
This is a Nautilus sample file, created using Ansible!
-rw-r-xr-x 1 apache apache ... /var/www/html/index.html
```

---

## What Each Part Does

| Part | Why it is needed |
|---|---|
| `hosts: all` | Runs on every server in the inventory |
| `become: yes` | Uses sudo/root to install packages and write to `/var/www/html` |
| `yum` | Installs httpd |
| `service` | Starts httpd and enables it on boot |
| `copy` with `content` | Creates the file with the first line |
| `lineinfile` with `insertbefore: BOF` | Adds the new line at the top (Beginning Of File) |
| `file` | Sets owner/group to `apache` and mode to `0655` |

## Common Problems

- **Indentation errors:** Use spaces only, never tabs.
- **Connection errors:** Check `ansible_user` and password/key in the inventory.
- **Quote the mode** as `'0655'` so Ansible does not misread it.
- **Order matters:** `copy` first, then `lineinfile`, then `file`. Otherwise `copy` would overwrite the welcome line. 
