# Day 93 - Challenge 
# KodeKloud Task: Ansible Conditionals (`when`)

## Task Description

The Nautilus DevOps team wants to use Ansible's conditionals to perform the following task:

An `inventory` file is already placed under `/home/thor/ansible` on the `jump host`, with all the Stratos DC app servers included.

Create a playbook `/home/thor/ansible/playbook.yml` and use Ansible's `when` conditional statements to do the following:

1. Copy `blog.txt` from `/usr/src/finance` on the jump host to **App Server 1** under `/opt/finance`. Owner and group must be `tony`, permissions `0655`.
2. Copy `story.txt` from `/usr/src/finance` on the jump host to **App Server 2** under `/opt/finance`. Owner and group must be `steve`, permissions `0655`.
3. Copy `media.txt` from `/usr/src/finance` on the jump host to **App Server 3** under `/opt/finance`. Owner and group must be `banner`, permissions `0655`.

**Notes:**
- Use the `ansible_nodename` variable from gathered facts with the `when` condition.
- Run the play for all hosts: `- hosts: all`.
- Validation runs `ansible-playbook -i inventory playbook.yml`, so the playbook must work with no extra arguments.

---

## Solution Steps

### Step 1: Go to the ansible directory

```bash
cd /home/thor/ansible
```

### Step 2: Check the inventory file

```bash
cat inventory
```

You should see the three servers: `stapp01`, `stapp02`, `stapp03`.

### Step 3: Check the exact node names

```bash
ansible -i inventory all -m setup -a "filter=ansible_nodename"
```

In this lab the values are exactly `stapp01`, `stapp02` and `stapp03` (not the long `.stratos.xfusioncorp.com` names). The `when` conditions must match these exactly.

### Step 4: Create the playbook

```bash
vi playbook.yml
```

Press `i` to enter insert mode, then paste:

```yaml
---
- hosts: all
  become: yes
  tasks:

    - name: Copy blog.txt to App Server 1
      copy:
        src: /usr/src/finance/blog.txt
        dest: /opt/finance/blog.txt
        owner: tony
        group: tony
        mode: '0655'
      when: ansible_nodename == "stapp01"

    - name: Copy story.txt to App Server 2
      copy:
        src: /usr/src/finance/story.txt
        dest: /opt/finance/story.txt
        owner: steve
        group: steve
        mode: '0655'
      when: ansible_nodename == "stapp02"

    - name: Copy media.txt to App Server 3
      copy:
        src: /usr/src/finance/media.txt
        dest: /opt/finance/media.txt
        owner: banner
        group: banner
        mode: '0655'
      when: ansible_nodename == "stapp03"
```

Save and exit: press `Esc`, type `:wq`, press `Enter`.

(If you need to clear an old version first: `Esc`, then `:%d`, `Enter`, then `i` and paste.)

### Step 5: Run the playbook

```bash
ansible-playbook -i inventory playbook.yml
```

Each server should show `changed` for its own task and `skipping` for the other two.

### Step 6: Verify

```bash
ansible -i inventory all -a "ls -l /opt/finance"
```

Expected output:

```
stapp01 | CHANGED | rc=0 >>
-rw-r-xr-x 1 tony   tony   ... blog.txt

stapp02 | CHANGED | rc=0 >>
-rw-r-xr-x 1 steve  steve  ... story.txt

stapp03 | CHANGED | rc=0 >>
-rw-r-xr-x 1 banner banner ... media.txt
```

`-rw-r-xr-x` is how `0655` looks, so it is correct.

---

## Tips

- Indentation matters in YAML. Use spaces only, never tabs.
- If every task shows `skipping`, the `when` value does not match the real `ansible_nodename`. Re-check Step 3.
- If you get a "directory does not exist" error, add this task before the copy tasks:

```yaml
    - name: Create /opt/finance
      file:
        path: /opt/finance
        state: directory
```
