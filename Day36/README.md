# Day 36 - Challenge 
### Task Description:
The Nautilus DevOps team is conducting application deployment tests on selected application servers. They require an **nginx container deployment** on **Application Server 1**.  

Instructions:
- SSH into **App Server 1** using user `tony` and password `Ir0nM@n`.
- Create a container named **nginx_1** using the `nginx:alpine` image.
- Ensure the container is in a **running state**.

---

### Solution:

# Step 1: SSH into Application Server 1
ssh tony@stapp01

# Step 2: Switch to root user (if needed)
sudo -i

# Step 3: Pull the nginx image with alpine tag
docker pull nginx:alpine

# Step 4: Run container named nginx_1 in detached mode
docker run -d --name nginx_1 nginx:alpine

# Step 5: Verify container is running
docker ps

