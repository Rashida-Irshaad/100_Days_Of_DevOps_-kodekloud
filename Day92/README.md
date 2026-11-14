# Day 92 - Challenge 
# KodeKloud Task: Ansible httpd Role with Jinja2 Template

## Task Description

One of the Nautilus DevOps team members is working on a role for `httpd` installation and configuration. Work is almost complete, but a jinja2 template for `index.html` needs to be added, and the relevant task must be added inside the role. The inventory file `~/ansible/inventory` is already present on the `jump host`.

Requirements:

a. Update `~/ansible/playbook.yml` to run the `httpd` role on `App Server 3`.

b. Create a jinja2 template `index.html.j2` under `/home/thor/ansible/role/httpd/templates/` with the line:
   `This file was created using Ansible on <respective server>` (e.g. `stapp01` for App Server 1).
   Do not hard code the server name. Use the `inventory_hostname` variable.

c. Add a task in `/home/thor/ansible/role/httpd/tasks/main.yml` to copy this template to `/var/www/html/index.html` on App Server 3. Permissions must be `0644`.

d. The user/group owner of `/var/www/html/index.html` must be the respective sudo user of the server (e.g. `tony` for `stapp01`).

Note: Validation removes `httpd` and `/var/www/html/index.html` from App Server 3, then runs:
`ansible-playbook -i inventory playbook.yml`
with no extra arguments. Everything is graded from the result of that run.

---

## Solution (run all on the jump host as user `thor`)

App Server 3 = `stapp03`, sudo user = `banner`.

### Step 1: Inspect the existing files

```bash
cd ~/ansible
cat inventory
cat playbook.yml
ls -R role/httpd
cat role/httpd/tasks/main.yml
```

### Step 2: Update the playbook (task a)

```bash
vi ~/ansible/playbook.yml
```

```yaml
---
- hosts: stapp03
  become: yes
  roles:
    - role/httpd
```

### Step 3: Create the Jinja2 template (task b)

```bash
vi ~/ansible/role/httpd/templates/index.html.j2
```

Content (one line):

```
This file was created using Ansible on {{ inventory_hostname }}
```

### Step 4: Update the role tasks (tasks c and d)

```bash
vi ~/ansible/role/httpd/tasks/main.yml
```

Make the whole file look like this. The install task must come first, because
it creates `/var/www/html`. Without it you get the error
"Destination directory /var/www/html does not exist".

```yaml
---
- name: Install httpd
  package:
    name: httpd
    state: present

- name: Start and enable httpd service
  service:
    name: httpd
    state: started
    enabled: yes

- name: Copy index.html template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
    owner: banner
    group: banner
    mode: '0644'
```

Use the `template` module (not `copy`) so the Jinja2 variable gets processed.

### Step 5: Run the playbook

```bash
cd ~/ansible
ansible-playbook -i inventory playbook.yml
```

Expected: three role tasks run and the recap shows `failed=0`.

### Step 6: Verify

```bash
ansible -i inventory stapp03 -m shell -a "ls -l /var/www/html/index.html; cat /var/www/html/index.html"
```

Expected:
- `-rw-r--r-- 1 banner banner /var/www/html/index.html`
- `This file was created using Ansible on stapp03`

---

## Troubleshooting

| Problem | Fix |
|---|---|
| `Destination directory /var/www/html does not exist` | httpd is not installed. Add the install task before the copy task. |
| `Could not match supplied host pattern` | Host name in playbook must match `cat inventory` exactly. |
| `Permission denied` | Make sure `become: yes` is in the playbook. |
| YAML syntax error | Use spaces only, never tabs. |
| Role not found | Run `ls role/` and use the exact path in `roles:`. |
