# Day 41 - Challenge 
TASK DESCRIPTION:
The Nautilus application development team wants a custom Docker image. You need to create a Dockerfile on App Server 3 under /opt/docker/ and use it to build an image with these requirements:
1. Base image should be ubuntu:24.04
2. Apache2 must be installed inside the image.
3. Apache should be configured to listen on port 8088 instead of the default 80.

SOLUTION STEPS:

# Step 1: SSH into App Server 3
ssh banner@stapp03
# (Password will be provided in the lab details)

# Step 2: Create the docker directory if it does not exist
sudo mkdir -p /opt/docker
cd /opt/docker

# Step 3: Manually create Dockerfile (note: D must be capital)
sudo nano Dockerfile
# Paste the following content inside and save the file:

FROM ubuntu:24.04

# Install apache2
RUN apt-get update && apt-get install -y apache2 && apt-get clean

# Change Apache default port from 80 to 8088
RUN sed -i 's/80/8088/' /etc/apache2/ports.conf \
    && sed -i 's/:80/:8088/' /etc/apache2/sites-enabled/000-default.conf

# Expose port 8088
EXPOSE 8088

# Start apache in foreground
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

# Save and exit (CTRL+O, Enter, CTRL+X in nano)

# Step 4: Build the custom image
sudo docker build -t custom-apache:latest /opt/docker

# Step 5: Run a container from the image to test
sudo docker run -d --name test_apache -p 8088:8088 custom-apache:latest

# Step 6: Verify Apache is running on 8088
curl http://localhost:8088

