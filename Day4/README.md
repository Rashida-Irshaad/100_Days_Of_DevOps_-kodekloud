# Day 4 - Challenge 
# Day 4 - Grant Executable Permissions to Bash Script

## Task Description
In a bid to automate backup processes, the **xFusionCorp Industries** sysadmin team has developed a new bash script named **xfusioncorp.sh**.  
While the script has been distributed to all necessary servers, it **lacks executable permissions** on App Server 1 within the **Stratos Datacenter**.  

Your task is to:

- Grant executable permissions to the `/tmp/xfusioncorp.sh` script on App Server 1.  
- Ensure that **all users** have the capability to execute it.

---

## Requirements
- **Script Path**: `/tmp/xfusioncorp.sh`  
- **Server**: App Server 1 (`stapp01`)  
- **User**: `tony`  
- **Permissions**: Executable for all users  

---

## Steps to Complete Task
1. **Login to App Server 1**:
    ```bash
    ssh tony@stapp01
    ```

2. **Grant Read & Execute Permissions to All Users**:
    ```bash
    sudo chmod a+rx /tmp/xfusioncorp.sh
    ```

3. **Verify Permissions**:
    ```bash
    ls -l /tmp/xfusioncorp.sh
    ```
    Expected output:
    ```
    -r-xr-xr-x 1 root root 40 Aug  9 18:29 /tmp/xfusioncorp.sh
    ```

4. **Optional: Test Script Execution**:
    ```bash
    /tmp/xfusioncorp.sh
    ```

--------

