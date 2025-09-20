# Day 43 - Challenge 
# TASK DESCRIPTION:
# The Nautilus DevOps team wants to host an application using nginx.
# Requirements:
#   a. Pull the nginx:alpine-perl docker image on Application Server 3.
#   b. Create a container named "blog" using this image.
#   c. Map host port 6100 to container port 80.
#   d. Keep the container in running state.

# ------------------ SOLUTION ------------------

# 1. Connect to App Server 3
ssh banner@stapp03

# 2. Pull the required docker image (nginx:alpine-perl)
docker pull nginx:alpine-perl

# 3. Create the container named "blog" (just creation, not started yet)
docker create --name blog -p 6100:80 nginx:alpine-perl

# 4. Start the container (so it runs in background)
docker start blog

# 5. Verify the container is running
docker ps

# 6. (Optional) Test nginx is accessible
curl http://localhost:6100

