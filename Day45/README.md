# Day 45 - Challenge 
### Task Description
The Nautilus DevOps team is working to create new images per requirements shared by the development team. One of the team members is working to create a Dockerfile on App Server 1 in Stratos DC. While working on it she ran into issues in which the docker build is failing and displaying errors. Look into the issue and fix it to build an image as per details mentioned below:  
a. The Dockerfile is placed on App Server 1 under `/opt/docker` directory.  
b. Fix the issues with this file and make sure it is able to build the image.  
c. Do not change base image, any other valid configuration within Dockerfile, or any of the data been used — for example, `index.html`.  

Note: Once you click on **FINISH**, all existing containers will be destroyed and a new image will be built from your Dockerfile.  

### Identified Issues
1. Used `IMAGE` instead of `FROM` for base image declaration.  
2. Used `ADD` instead of `RUN` for executing `sed` commands.  
3. Missing full paths in `sed` commands for Apache configuration files.  

### Solution Steps
1. **SSH into App Server 1**  
   ```bash
   ssh tony@stapp01
  

2. **Navigate to Dockerfile location**  
   ```bash
   cd /opt/docker
   ```

3. **Edit the Dockerfile**  
   ```bash
   vi Dockerfile
   ```

4. **Fix the Dockerfile content** (replace with below):  
   ```Dockerfile
   FROM httpd:2.4.43

   # Correct way: use RUN for commands, ADD/COPY for files
   ADD sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf
   ADD sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' /usr/local/apache2/conf/httpd.conf
   ADD sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' /usr/local/apache2/conf/httpd.conf
   ADD sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' /usr/local/apache2/conf/httpd.conf

   COPY certs/server.crt /usr/local/apache2/conf/server.crt
   COPY certs/server.key /usr/local/apache2/conf/server.key
   COPY html/index.html /usr/local/apache2/htdocs/
   ```
5. **Save and exit**  
   - Press `ESC`, then type `:wq` and hit Enter.

6. **Build the Docker image** 

   docker build -t nautilus-web-app .
   #   If pull also times out, but the lab has httpd:latest already cached, temporarily change the first line of Dockerfile:

   FROM httpd:latest
   # Then:

   docker build -t nautilus-web-app .
   

7. **Run and Test Container**  
 
   # Start container
   docker run -d -p 8080:8080 --name test-container nautilus-web-app

   # Test web server
   curl http://localhost:8080

   # Check container status
   docker ps

---


