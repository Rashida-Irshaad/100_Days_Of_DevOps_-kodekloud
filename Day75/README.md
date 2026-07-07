# Day 75 - Challenge 
# Day 72 - Jenkins: Add App Servers as SSH Build Agents (KodeKloud)

## Task Description

The Nautilus DevOps team has installed and configured a new Jenkins server in the Stratos Datacenter. The requirement is to add all three application servers as SSH build agents (slave nodes) in Jenkins so that Jenkins can execute jobs remotely on these servers.

### Requirements

- Create three Jenkins SSH build agents with the following names:
  - `App_server_1`
  - `App_server_2`
  - `App_server_3`
- Configure labels:
  - `App_server_1` → `stapp01`
  - `App_server_2` → `stapp02`
  - `App_server_3` → `stapp03`
- Configure remote root directories:
  - `/home/tony/jenkins`
  - `/home/steve/jenkins`
  - `/home/banner/jenkins`
- Ensure all nodes are online and connected successfully.

---

# Solution

## Step 1: Generate SSH Key on Jenkins Server

Login to the Jenkins server as the `jenkins` user and generate an SSH key pair.

```bash
ssh-keygen -t rsa -b 4096 -N "" -f ~/.ssh/id_rsa
```

Verify the key files:

```bash
ls ~/.ssh
```

Expected output:

```text
id_rsa
id_rsa.pub
known_hosts
```

Display the public key:

```bash
cat ~/.ssh/id_rsa.pub
```

Copy the complete public key.

---

## Step 2: Configure Passwordless SSH on All App Servers

### App Server 1

```bash
ssh tony@stapp01
mkdir -p ~/.ssh
chmod 700 ~/.ssh
vi ~/.ssh/authorized_keys
```

Paste the Jenkins public key.

Save and exit:

```
:wq
```

Set permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
mkdir -p /home/tony/jenkins
exit
```

---

### App Server 2

```bash
ssh steve@stapp02
mkdir -p ~/.ssh
chmod 700 ~/.ssh
vi ~/.ssh/authorized_keys
```

Paste the same Jenkins public key.

Save:

```
:wq
```

Set permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
mkdir -p /home/steve/jenkins
exit
```

---

### App Server 3

```bash
ssh banner@stapp03
mkdir -p ~/.ssh
chmod 700 ~/.ssh
vi ~/.ssh/authorized_keys
```

Paste the same Jenkins public key.

Save:

```
:wq
```

Set permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
mkdir -p /home/banner/jenkins
exit
```

---

## Step 3: Verify Passwordless SSH

Run the following commands from the Jenkins server:

```bash
ssh tony@stapp01 hostname
ssh steve@stapp02 hostname
ssh banner@stapp03 hostname
```

Expected output:

```text
stapp01
stapp02
stapp03
```

No password prompt should appear.

---

## Step 4: Display the Private Key

Display the private key:

```bash
cat ~/.ssh/id_rsa
```

Copy the entire private key including:

```text
-----BEGIN OPENSSH PRIVATE KEY-----
...
-----END OPENSSH PRIVATE KEY-----
```

This key will be used inside Jenkins credentials.

---

# Jenkins Configuration

## Step 5: Create SSH Credentials

Navigate to:

```
Manage Jenkins
→ Credentials
→ System
→ Global Credentials (unrestricted)
→ Add Credentials
```

Create the following credentials.

### Credential 1

- Kind: SSH Username with private key
- Username: `tony`
- Private Key: Enter directly
- Paste Jenkins private key
- ID: `tony-ssh`

---

### Credential 2

- Kind: SSH Username with private key
- Username: `steve`
- Private Key: Enter directly
- Paste the same private key
- ID: `steve-ssh`

---

### Credential 3

- Kind: SSH Username with private key
- Username: `banner`
- Private Key: Enter directly
- Paste the same private key
- ID: `banner-ssh`

---

## Step 6: Create Jenkins Nodes

Navigate to:

```
Manage Jenkins
→ Nodes
→ New Node
```

---

### Node 1

- Node Name: `App_server_1`
- Permanent Agent

Configuration:

- Remote Root Directory:
  ```
  /home/tony/jenkins
  ```
- Labels:
  ```
  stapp01
  ```
- Launch Method:
  ```
  Launch agents via SSH
  ```
- Host:
  ```
  stapp01
  ```
- Credentials:
  ```
  tony-ssh
  ```
- Host Key Verification Strategy:
  ```
  Non verifying Verification Strategy
  ```

Save.

---

### Node 2

- Node Name: `App_server_2`

Configuration:

- Remote Root Directory:
  ```
  /home/steve/jenkins
  ```
- Labels:
  ```
  stapp02
  ```
- Launch Method:
  ```
  Launch agents via SSH
  ```
- Host:
  ```
  stapp02
  ```
- Credentials:
  ```
  steve-ssh
  ```
- Host Key Verification Strategy:
  ```
  Non verifying Verification Strategy
  ```

Save.

---

### Node 3

- Node Name: `App_server_3`

Configuration:

- Remote Root Directory:
  ```
  /home/banner/jenkins
  ```
- Labels:
  ```
  stapp03
  ```
- Launch Method:
  ```
  Launch agents via SSH
  ```
- Host:
  ```
  stapp03
  ```
- Credentials:
  ```
  banner-ssh
  ```
- Host Key Verification Strategy:
  ```
  Non verifying Verification Strategy
  ```

Save.

---

## Step 7: Troubleshooting

Initially, the nodes failed with:

```
UnsupportedClassVersionError
```

Reason:

The Jenkins controller was running Java 17 while the agent machines were using Java 11.

Solution:

Install Java 17 (or configure the agent to use an existing Java 17 installation) on the affected app servers and reconnect the nodes.

---

## Step 8: Verify the Nodes

Navigate to:

```
Manage Jenkins
→ Nodes
```

Verify all agents are online:

| Node Name | Label | Remote Root Directory | Status |
|-----------|-------|-----------------------|--------|
| App_server_1 | stapp01 | /home/tony/jenkins | 🟢 Online |
| App_server_2 | stapp02 | /home/steve/jenkins | 🟢 Online |
| App_server_3 | stapp03 | /home/banner/jenkins | 🟢 Online |

---

# Result

Successfully configured all three application servers as Jenkins SSH build agents using passwordless SSH authentication. All nodes connected successfully and showed an **Online** status, allowing Jenkins to execute jobs remotely on each application server.
