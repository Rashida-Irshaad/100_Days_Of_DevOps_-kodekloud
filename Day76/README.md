# Day 76 - Challenge 
# Jenkins Project-Based Permissions for Existing Job (KodeKloud)

## Task Description

The DevOps team at xFusionCorp Industries needs to grant specific permissions to two developers for an existing Jenkins job named **Packages**.

### Requirements

- Login to Jenkins using:
  - Username: `admin`
  - Password: `Adm!n321`
- There is an existing Jenkins job named **Packages**.
- There are two existing users:
  - `sam` / `sam@pass12345`
  - `rohan` / `rohan@pass12345`
- Configure permissions as follows:

### User Permissions

**sam**
- Build
- Configure
- Read

**rohan**
- Build
- Cancel
- Configure
- Read
- Update
- Tag

### Additional Requirement

- Select **Inherit permissions from parent ACL** under the inheritance strategy.
- Do not modify any other job configuration.

---

# Solution

## Step 1: Login to Jenkins

- Open the Jenkins dashboard.
- Login using:
  - Username: `admin`
  - Password: `Adm!n321`

---

## Step 2: Install Required Plugins (if not already installed)

Navigate to:

```
Manage Jenkins
→ Plugins
→ Available Plugins
```

Install the following plugins if they are missing:

- Matrix Authorization Strategy
- Authorize Project (if required by the lab)

Restart Jenkins after installation.

---

## Step 3: Configure Global Security

Go to:

```
Manage Jenkins
→ Security
```

Under **Authorization**, select:

```
Project-based Matrix Authorization Strategy
```

Grant the following global permission:

### sam

- Overall → Read

### rohan

- Overall → Read

Click **Save**.

> **Note:** Without **Overall → Read**, users cannot access Jenkins even if job permissions are assigned.

---

## Step 4: Configure Project Permissions

Open the existing job:

```
Packages
→ Configure
```

Enable **Project-based Security** (if available).

Under **Inheritance Strategy**, select:

```
Inherit permissions from parent ACL
```

---

## Step 5: Add User sam

Click **Add User**.

Enter:

```
sam
```

Grant only the following permissions:

- Job → Read
- Job → Build
- Job → Configure

---

## Step 6: Add User rohan

Click **Add User**.

Enter:

```
rohan
```

Grant the following permissions:

- Job → Read
- Job → Build
- Job → Cancel
- Job → Configure
- Run → Update
- SCM → Tag

---

## Step 7: Save Configuration

Click **Save**.

---

## Step 8: Verify Permissions

Logout from Jenkins.

Login as:

```
Username: sam
Password: sam@pass12345
```

Verify that **sam** can:

- View the Packages job
- Build the job
- Configure the job

Logout.

Login as:

```
Username: rohan
Password: rohan@pass12345
```

Verify that **rohan** can:

- View the Packages job
- Build the job
- Cancel running builds
- Configure the job
- Update build information
- Tag SCM (if applicable)

---

# Result

Successfully configured project-based permissions for the existing **Packages** Jenkins job.

Permissions assigned:

### sam

- Overall → Read
- Job → Read
- Job → Build
- Job → Configure

### rohan

- Overall → Read
- Job → Read
- Job → Build
- Job → Cancel
- Job → Configure
- Run → Update
- SCM → Tag

Inheritance Strategy:

```
Inherit permissions from parent ACL
```

The Jenkins job permissions were successfully configured without modifying any other job settings.
