# Day 35 - Challenge 
### Task Description:
The Nautilus DevOps team aims to containerize various applications following a recent meeting with the application development team. They intend to conduct testing with the following steps:

- SSH into **App Server 3** using user `banner` and password `Bl@kW`.
- Install **docker-ce** and **docker compose** packages on App Server 3.
- Initiate the **docker service**.
- Verify installation.

---

### Solution:

# Step 1: SSH into App Server 3
ssh banner@stapp03

# Step 2: Switch to root user
sudo -i

# Step 3: Add Docker CE official repo
dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# Step 4: Install Docker CE and Docker Compose plugin
dnf install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Step 5: Start and enable Docker service
systemctl enable docker
systemctl start docker

# Step 6: Verify installation
docker --version
docker compose version   # notice: "docker compose" (with space), not "docker-compose"

