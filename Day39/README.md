# Day 39 - Challenge 
# Task Description:
# One of the Nautilus developers was testing new changes on a container.
# He wants to keep a backup of his changes.  
# The DevOps team needs to create a new image named ecommerce:devops 
# on Application Server 2 from the running container ubuntu_latest.

# 1. SSH into Application Server 2
ssh steve@stapp02
# (Password will be provided in your task)

# 2. Check that the container ubuntu_latest is running
sudo docker ps | grep ubuntu_latest

# 3. Create a new image named ecommerce:devops from this container
sudo docker commit ubuntu_latest ecommerce:devops

# 4. Verify that the new image was created
sudo docker images | grep ecommerce

