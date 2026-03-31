# Day 37 - Challenge 
# Task Description:
# The Nautilus DevOps team possesses confidential data on App Server 1 in the Stratos Datacenter. 
# A container named ubuntu_latest is running on the same server.
# You need to copy an encrypted file /tmp/nautilus.txt.gpg from the docker host 
# into the ubuntu_latest container at /home/. Ensure the file is not modified during this operation.

# 1. SSH into Application Server 1
ssh tony@stapp01
# (Password will be provided in your task)

# 2. Verify the running container
docker ps
# Look for a container named 'ubuntu_latest'

# 3. Copy the encrypted file from the host to the container
sudo docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/home/

# 4. Verify the file inside the container
sudo docker exec -it ubuntu_latest ls -l /home/
# You should see nautilus.txt.gpg present with the same size (unchanged)
