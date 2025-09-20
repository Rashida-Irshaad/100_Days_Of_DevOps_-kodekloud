# Day 44 - Challenge 
### 🚀 Task Description
The Nautilus application development team shared static website content that needs to be hosted on the `httpd` web server using a containerized platform. The DevOps team must set up the environment as follows:

- Use App Server 2 in Stratos DC.  
- Create a container named **httpd** using a Docker Compose file located at `/opt/docker/docker-compose.yml`.  
- Use the latest `httpd` image.  
- Map port `80` of the container to port `5001` of the host.  
- Mount the host directory `/opt/itadmin` to the container directory `/usr/local/apache2/htdocs`.  
- Do not modify data in `/opt/itadmin`.  

---

### ✅ Solution

1. **Create the Docker Compose file**:
   ```bash

   sudo vi /opt/docker/docker-compose.yml
   ```

   Add the following content:
   ```yaml
   version: '3'
   services:
     webapp:
       image: httpd:latest
       container_name: httpd
       ports:
         - "5001:80"
       volumes:
         - /opt/itadmin:/usr/local/apache2/htdocs
   ```

2. **Deploy the container**:
   ```bash
   cd /opt/docker
   sudo docker compose up -d
   ```

3. **Verify container is running**:
   ```bash
   sudo docker ps
   ```

   You should see a container named **httpd** running on port `5001`.



