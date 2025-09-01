# Day 2 - Challenge 
# Day 2 - Temporary User Account with Expiry Date

## Task Description
As part of a temporary assignment to the Nautilus project, a developer named **ravi** requires access for a limited duration.  
Your task is to create a temporary user account with an expiry date to ensure smooth access management.

---

## Requirements
- **Username**: `ravi` (all lowercase as per standard protocol)
- **Server**: App Server 1 (`stapp01`)
- **Expiry Date**: `2023-12-07`

---

## Steps to Complete Task
1. **Login to App Server 1**:
    ```bash
    ssh tony@stapp01
    ```

2. **Create User with Expiry Date**:
    ```bash
    sudo useradd -e 2023-12-07 ravi
    ```

3. **Verify User Details**:
    ```bash
    sudo chage -l ravi
    ```

4. **Exit the Server**:
    ```bash
    exit
    ```

---

