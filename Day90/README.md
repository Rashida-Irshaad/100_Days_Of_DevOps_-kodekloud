# Day 90 - Challenge # KodeKloud Task: Create Files with ACL Using Ansible (Stratos DC)

## Task Description

There are some files that need to be created on all app servers in `Stratos DC`. The Nautilus DevOps team wants these files to be owned by user `root` only, however, they also want the app-specific user to have a set of permissions on these files. All tasks must be done using Ansible only, so a playbook is needed.

Create a playbook named `playbook.yml` under `/home/thor/ansible` directory on the `jump host`. An `inventory` file is already present under `/home/thor/ansible` on the `Jump Server` itself.

1. Create an empty file `blog.txt` under `/opt/finance/` on app server 1. Using `acl`, provide read `(r)` permissions to group `tony` (`entity` is `tony` and `etype` is `group`).
2. Create an empty file `story.txt` under `/opt/finance/` on app server 2. Using `acl`, provide read + write `(rw)` permissions to user `steve` (`entity` is `steve` and `etype` is `user`).
3. Create an empty file `media.txt` under `/opt/finance/` on app server 3. Using `acl`, provide read + write `(rw)` permissions to group `banner` (`entity` is `banner` and `etype` is `group`).

**Note:** Validation will run the playbook using `ansible-playbook -i inventory playbook.yml`, so make sure the playbook works this way without passing any extra arguments.

---

## Solution Steps

### Step 1: Go to the ansible directory and check the inventory

```bash
cd /home/thor/ansible
cat inventory
```

Note the host names (usually `stapp01`, `stapp02`, `stapp03`). Use the exact same names in the playbook.

Typical inventory:

```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```

### Step 2: Create the playbook

```bash
vi playbook.yml
```

Press `i` to enter insert mode and paste:

```yaml
---
- name: Create blog.txt on app server 1
  hosts: stapp01
  become: yes
  tasks:
    - name: Create empty file blog.txt
      ansible.builtin.file:
        path: /opt/finance/blog.txt
        state: touch
        owner: root
        group: root

    - name: Give read permission to group tony
      ansible.posix.acl:
        path: /opt/finance/blog.txt
        entity: tony
        etype: group
        permissions: r
        state: present

- name: Create story.txt on app server 2
  hosts: stapp02
  become: yes
  tasks:
    - name: Create empty file story.txt
      ansible.builtin.file:
        path: /opt/finance/story.txt
        state: touch
        owner: root
        group: root

    - name: Give read+write permission to user steve
      ansible.posix.acl:
        path: /opt/finance/story.txt
        entity: steve
        etype: user
        permissions: rw
        state: present

- name: Create media.txt on app server 3
  hosts: stapp03
  become: yes
  tasks:
    - name: Create empty file media.txt
      ansible.builtin.file:
        path: /opt/finance/media.txt
        state: touch
        owner: root
        group: root

    - name: Give read+write permission to group banner
      ansible.posix.acl:
        path: /opt/finance/media.txt
        entity: banner
        etype: group
        permissions: rw
        state: present
```

Save and exit: press `Esc`, type `:wq`, press `Enter`.

### Step 3: Run the playbook

```bash
ansible-playbook -i inventory playbook.yml
```

All tasks should show `ok` or `changed` with `failed=0`.

### Step 4: Verify (optional)

```bash
ansible stapp01 -i inventory -b -m command -a "getfacl /opt/finance/blog.txt"
ansible stapp02 -i inventory -b -m command -a "getfacl /opt/finance/story.txt"
ansible stapp03 -i inventory -b -m command -a "getfacl /opt/finance/media.txt"
```

Expected output lines:

- `group:tony:r--`
- `user:steve:rw-`
- `group:banner:rw-`

---

## Explanation

| Part | Meaning |
|---|---|
| `hosts: stapp01` | Runs only on app server 1 |
| `become: yes` | Uses sudo so the file can be owned by root |
| `file` + `state: touch` | Creates an empty file |
| `owner: root` | Keeps the file owned by root only |
| `acl` module | Sets the extra permissions |
| `etype` | Type of entity: `user` or `group` |
| `entity` | Name of that user or group |
| `permissions` | `r` is read, `rw` is read and write |

## Common Problems

- **Host names don't match:** Use the exact names from your `inventory` file.
- **`ansible.posix.acl` not found:** Use just `acl:` instead (works on older Ansible versions).
- **`/opt/finance` doesn't exist:** Add a task before the file task that creates it with `state: directory`.
- **Become/sudo password error:** Check the inventory for `ansible_become_pass`.


