# Day 89 - Challenge 
# KodeKloud Task: Install and Start httpd with Ansible

## Task Description

Developers are looking for dependencies to be installed and run on Nautilus app servers in Stratos DC. They have shared some requirements with the DevOps team. Because we are now managing packages installation and services management using Ansible, some playbooks need to be created and tested. As per details mentioned below please complete the task:

a. On `jump host` create an Ansible playbook `/home/thor/ansible/playbook.yml` and configure it to install `httpd` on all app servers.
b. After installation make sure to start and enable `httpd` service on all app servers.
c. The inventory `/home/thor/ansible/inventory` is already there on `jump host`.
d. Make sure user `thor` should be able to run the playbook on `jump host`.

**Note:** Validation will try to run playbook using command `ansible-playbook -i inventory playbook.yml` so please make sure playbook works this way, without passing any extra arguments.

---

## Solution Steps

### Step 1: Go to the ansible directory

On the jump host (as user `thor`):

```bash
cd /home/thor/ansible
```

### Step 2: Check the inventory file

```bash
cat inventory
```

Example output (your hosts and passwords will differ):

```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

If there are no group headers, use `hosts: all` in the playbook.

### Step 3: Create the playbook

```bash
vi playbook.yml
```

Press `i` to enter insert mode, then paste:

```yaml
---
- name: Install and start httpd on all app servers
  hosts: all
  become: yes
  tasks:
    - name: Install httpd package
      yum:
        name: httpd
        state: present

    - name: Start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: yes
```

Save and exit: press `Esc`, type `:wq`, press `Enter`.

### What each part means

| Line | Meaning |
|------|---------|
| `hosts: all` | Run on every server in the inventory |
| `become: yes` | Use sudo (needed to install packages) |
| `yum: name: httpd state: present` | Install httpd |
| `service: state: started` | Start the service now |
| `service: enabled: yes` | Start it automatically on boot |

### Step 4: Make sure thor can run it

```bash
ls -l /home/thor/ansible/
```

If the files are owned by root, fix it:

```bash
sudo chown -R thor:thor /home/thor/ansible
```

### Step 5: Run the playbook

Run it exactly the way validation will:

```bash
ansible-playbook -i inventory playbook.yml
```

### Step 6: Check the output

Look at the **PLAY RECAP**. It should show `failed=0` and `unreachable=0` for every server:

```
stapp01 : ok=3  changed=2  unreachable=0  failed=0
stapp02 : ok=3  changed=2  unreachable=0  failed=0
stapp03 : ok=3  changed=2  unreachable=0  failed=0
```

---

## Troubleshooting

- **Syntax error:** Use spaces only (never tabs). Check with:
  ```bash
  ansible-playbook -i inventory playbook.yml --syntax-check
  ```
- **"Missing sudo password" error:** Add `ansible_become_pass=<password>` to the inventory lines, or confirm the users have passwordless sudo.
- **Host key checking error:** Create `/home/thor/ansible/ansible.cfg` with:
  ```ini
  [defaults]
  host_key_checking = False
  ```
- **Group in inventory:** If the inventory has a group like `[stapp]`, you can use `hosts: stapp`, but `hosts: all` works if all hosts are app servers.

Once the recap shows no failures, click **Check** in KodeKloud.