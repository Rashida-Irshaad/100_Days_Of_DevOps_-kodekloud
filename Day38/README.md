# Day 38 - Challenge 
# Task Description:
# Nautilus project developers are planning to start testing on a new project.
# They want to test containerized environment application features.
# As per requirements, on App Server 2 in Stratos DC:
#   a. Pull the busybox:musl image.
#   b. Re-tag (create new tag) this image as busybox:local.

# 1. SSH into Application Server 2
ssh steve@stapp02
# (Password will be provided in your task)

# 2. Pull the busybox:musl image from Docker Hub
sudo docker pull busybox:musl

# 3. Re-tag the pulled image as busybox:local
sudo docker tag busybox:musl busybox:local

# 4. Verify both tags exist
sudo docker images | grep busybox
# You should see busybox with tags 'musl' and 'local'

