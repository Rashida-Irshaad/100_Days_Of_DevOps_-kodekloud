# Day 88 - Challenge 
# KodeKloud Task: Install and Set Up httpd Using Ansible

## Task Description

The Nautilus DevOps team wants to install and set up a simple `httpd` web server on all app servers in `Stratos DC`. Additionally, they want to deploy a sample web page for now using Ansible only. Therefore, write the required playbook to complete this task.

We already have an `inventory` file under `/home/thor/ansible` directory on `jump host`. Create a `playbook.yml` under `/home/thor/ansible` directory on `jump host` itself.

1. Using the playbook, install `httpd` web server on all app servers. Additionally, make sure its service should up and running.
2. Using `blockinfile` Ansible module add some content in `/var/www/html/index.html` file. Below is the content:
   ```
   Welcome to XfusionCorp!
   This is Nautilus sample file, created using Ansible!
   Please do not modify this file manually!
   ```
3. The `/var/www/html/index.html` file's user and group `owner` should be `apache` on all app servers.
4. The `/var/www/html/index.html` file's permissions should be `0744` on all app servers.

**Note:**
- Validation will try to run the playbook using the command `ansible-playbook -i inventory playbook.yml`, so make sure the playbook works this way without passing any extra arguments.
- Do not use any custom or empty `marker` for the `blockinfile` module.

---

## Solution

### Step 1: Go to the ansible directory on the jump host

```bash
cd /home/thor/ansible
ls
cat inventory
```

Check that the `inventory` file lists all the app servers (stapp01, stapp02, stapp03). We will use `hosts: all`, so the group name doesn't matter.

### Step 2: Create the playbook

```bash
vi playbook.yml
```

Press `i` to enter insert mode, then paste this:

```yaml
---
- name: Install and configure httpd on app servers
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

    - name: Add content to index.html using blockinfile
      blockinfile:
        path: /var/www/html/index.html
        create: yes
        block: |
          Welcome to XfusionCorp!
          This is Nautilus sample file, created using Ansible!
          Please do not modify this file manually!
        owner: apache
        group: apache
        mode: '0744'
```

Save and exit: press `Esc`, type `:wq`, then press `Enter`.

### Step 3: Understand what each part does

| Part | Meaning |
|---|---|
| `hosts: all` | Runs on every server in the inventory |
| `become: yes` | Uses sudo/root privileges |
| `yum` | Installs the `httpd` package |
| `service` | Starts httpd and enables it at boot |
| `blockinfile` | Adds the 3 lines to `index.html` |
| `create: yes` | Creates the file if it doesn't exist |
| `owner` / `group` | Sets both to `apache` |
| `mode: '0744'` | Sets the file permissions |

Do not add a `marker` option. The default marker is what the task requires.

### Step 4: Check the syntax (optional)

```bash
ansible-playbook -i inventory playbook.yml --syntax-check
```

### Step 5: Run the playbook

Run it exactly as the validation will:

```bash
ansible-playbook -i inventory playbook.yml
```

You should see `ok` or `changed` for every task and `failed=0` in the recap for each server.

### Step 6: Verify (optional)

```bash
ansible all -i inventory -b -m shell -a "ls -l /var/www/html/index.html; cat /var/www/html/index.html"
```

Expected result:
- Owner and group are `apache`
- Permissions are `-rwxr--r--` (0744)
- The file contains the 3 lines between `# BEGIN ANSIBLE MANAGED BLOCK` and `# END ANSIBLE MANAGED BLOCK`

---

## Common Problems

- **YAML indentation errors:** Use spaces only, never tabs. Keep the indentation exactly as shown.
- **Permission or sudo errors:** Check that the `inventory` file has the user and password or key set for each host. If `become` asks for a password, check `ansible_become_password` in the inventory.
- **Pasting in vi messes up indentation:** Type `:set paste` before pasting in insert mode.
