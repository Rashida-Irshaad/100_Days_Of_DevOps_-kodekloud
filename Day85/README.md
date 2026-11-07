# Day 85 - Challenge 
# KodeKloud Task: Ansible File Creation on App Servers (Stratos DC)

## Task Description

The Nautilus DevOps team is testing various Ansible modules on servers in Stratos DC. They're currently focusing on file creation on remote hosts using Ansible. Here are the details:

a. Create an inventory file `~/playbook/inventory` on jump host and include all app servers.
b. Create a playbook `~/playbook/playbook.yml` to create a blank file `/tmp/opt.txt` on all app servers.
c. Set the permissions of the `/tmp/opt.txt` file to `0744`.
d. Ensure the user/group owner of the `/tmp/opt.txt` file is `tony` on app server 1, `steve` on app server 2 and `banner` on app server 3.

Note: Validation will execute the playbook using the command `ansible-playbook -i inventory playbook.yml`, so ensure the playbook functions correctly without any additional arguments.

---

## Solution Steps

### Step 1: Create the playbook directory

```bash
mkdir -p ~/playbook
cd ~/playbook
```

### Step 2: Create the inventory file

Each server goes on a single line. The `file_owner` variable stores the owner name for that server.
Check the passwords against your task page, since they can differ between tasks.

```bash
cat > inventory << 'EOF'
stapp01 ansible_host=stapp01 ansible_user=tony ansible_ssh_pass=Ir0nM@n ansible_ssh_common_args='-o StrictHostKeyChecking=no' file_owner=tony
stapp02 ansible_host=stapp02 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no' file_owner=steve
stapp03 ansible_host=stapp03 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no' file_owner=banner
EOF
```

### Step 3: Create the playbook

Using `cat` with a heredoc avoids `vi` auto-indent problems.

```bash
cat > playbook.yml << 'EOF'
---
- name: Create blank file on app servers
  hosts: all
  become: yes
  tasks:
    - name: Create /tmp/opt.txt with correct owner
      file:
        path: /tmp/opt.txt
        state: touch
        mode: '0744'
        owner: "{{ file_owner }}"
        group: "{{ file_owner }}"
EOF
```

### Step 4: Run the playbook

```bash
cd ~/playbook
ansible-playbook -i inventory playbook.yml
```

Expected result: all three servers show `ok` or `changed`, and the recap shows `failed=0`.

### Step 5: Verify

```bash
ansible all -i inventory -m shell -a "ls -l /tmp/opt.txt"
```

Expected output:

```
stapp01 | CHANGED | rc=0 >>
-rwxr--r-- 1 tony   tony   0 ... /tmp/opt.txt
stapp02 | CHANGED | rc=0 >>
-rwxr--r-- 1 steve  steve  0 ... /tmp/opt.txt
stapp03 | CHANGED | rc=0 >>
-rwxr--r-- 1 banner banner 0 ... /tmp/opt.txt
```

---

## How It Works

- `state: touch` creates an empty file, like the `touch` command.
- `mode: '0744'` gives the owner read, write and execute, and everyone else read only. Keep the quotes.
- `owner` and `group` use `{{ file_owner }}`, which Ansible replaces with each host's value from the inventory (tony, steve or banner).
- `become: yes` runs the task with sudo so ownership can be changed.

## Common Problems

- **Authentication failed:** Check that the passwords in the inventory match your task page.
- **sshpass not found:** Run `sudo yum install -y sshpass`.
- **Inventory not found:** Run the command from inside `~/playbook`.

