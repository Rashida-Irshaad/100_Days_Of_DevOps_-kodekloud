# Day 40 - Challenge 
# Task: Configure Apache inside kkloud container on App Server 3
# -------------------------------------------------------------
# a) Install apache2 inside kkloud container
# b) Change Apache listen port to 6000 (not bound to specific IP/host)
# c) Make sure Apache service is running and container stays up

# Step 1: SSH into App Server 3
ssh banner@stapp03
# (Password will be provided in your task instructions)

# Step 2: Check running containers to confirm 'kkloud' is up
sudo docker ps

# Step 3: Access the running container's shell
sudo docker exec -it kkloud bash

# Step 4: Update package list inside container
apt-get update

# Step 5: Install Apache2
apt-get install -y apache2

# Step 6: Change Apache listen port to 6000
sed -i 's/80/6000/' /etc/apache2/ports.conf

# Step 7: Restart Apache inside the container
service apache2 restart

# Step 8: Exit the container
exit

# Step 9: Ensure container remains running
sudo docker ps

