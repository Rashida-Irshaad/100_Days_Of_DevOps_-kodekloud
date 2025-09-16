# Day 42 - Challenge 
# TASK DESCRIPTION:
# The Nautilus DevOps team needs to create a docker network for future use.
# Requirements:
#   a. Create a docker network named "beta" on App Server 2 in Stratos DC.
#   b. Use the "bridge" driver.
#   c. Configure subnet as 172.168.0.0/24.
#   d. Configure ip-range as 172.168.0.0/24.

# ------------------ SOLUTION ------------------

# 1. Connect to App Server 2 using SSH
ssh steve@stapp02

# 2. Create the docker network with required settings
docker network create \
  --driver=bridge \
  --subnet=172.168.0.0/24 \
  --ip-range=172.168.0.0/24 \
  beta

# 3. Verify the network was created
docker network ls

# 4. Inspect the network details (to confirm subnet and ip-range)
docker network inspect beta
