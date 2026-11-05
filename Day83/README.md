# Day 83 - Challenge 
# KodeKloud Task: Complete Ansible Playbook on Jump Host

## Task Description

An Ansible playbook needs completion on the `jump host`, where a team member left off. Below are the details:

1. The inventory file `/home/thor/ansible/inventory` requires adjustments. The playbook must run on `App Server 2` in `Stratos DC`. Update the inventory accordingly.
2. Create a playbook `/home/thor/ansible/playbook.yml`. Include a task to create an empty file `/tmp/file.txt` on `App Server 2`.

**Note:** Validation will run the playbook using the command `ansible-playbook -i inventory playbook.yml`. Ensure the playbook works without any additional arguments.

---

## Solution (run on the jump host as user `thor`)

### Step 1: Go to the ansible directory

cd /home/thor/ansible

### Step 2: View the current inventory

cat inventory

### Step 3: App Server 2 details (Stratos DC)

| Item     | Value   |
|----------|---------|
| Hostname | stapp02 |
| User     | steve   |
| Password | Am3ric@ |

(If your task page shows different credentials, use those.)

### Step 4: Edit the inventory file

vi inventory

Delete everything and add this single line:

stapp02 ansible_host=stapp02 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no'

Save and exit: press Esc, type :wq, press Enter.

### Step 5: Create the playbook

vi playbook.yml

Paste this:

---
- name: Create empty file on App Server 2
  hosts: stapp02
  tasks:
    - name: Create /tmp/file.txt
      file:
        path: /tmp/file.txt
        state: touch

Save and exit: press Esc, type :wq, press Enter.

Note: Use spaces only (no tabs) in YAML.

### Step 6: Run the playbook

ansible-playbook -i inventory playbook.yml

Success looks like: stapp02 : ok=2 changed=1 unreachable=0 failed=0

### Step 7 (optional): Verify the file

ansible -i inventory stapp02 -m shell -a "ls -l /tmp/file.txt"

---

## Troubleshooting

- sshpass error: check with `which sshpass` (normally pre-installed).
- Permission denied: re-check user and password in the inventory.
- YAML error: check indentation and make sure there are no tabs.
- Host not found: the `hosts:` value in the playbook must match the inventory name (`stapp02`).

Finally, click **Check** on the KodeKloud task page.