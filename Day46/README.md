# Day 46 - Challenge 
### Task Description
The Nautilus Application development team recently finished development of one of the apps that they want to deploy on a containerized platform. The Nautilus Application development and DevOps teams met to discuss some of the basic pre-requisites and requirements to complete the deployment. The team wants to test the deployment on one of the app servers before going live and set up a complete containerized stack using a docker compose file.  

On **App Server 1** in Stratos Datacenter, create a docker compose file `/opt/security/docker-compose.yml`. The compose should deploy two services (web and DB), and each service should deploy a container as per the details below:  

**Web service**:  
- Container name must be `php_host`.  
- Use image `php` with any apache tag.  
- Map `php_host` container's port `80` with host port `3000`.  
- Map `php_host` container's `/var/www/html` volume with host volume `/var/www/html`.  

**DB service**:  
- Container name must be `mysql_host`.  
- Use image `mariadb` with any tag (preferably latest).  
- Map `mysql_host` container's port `3306` with host port `3306`.  
- Map `mysql_host` container's `/var/lib/mysql` volume with host volume `/var/lib/mysql`.  
- Set `MYSQL_DATABASE=database_host` and use any **custom user** (not root) with a complex password for DB connections.  

After running `docker-compose up`, you should be able to access the app with:  
```bash
curl <server-ip>:3000/
```

---

### Solution Steps

1. **SSH into App Server 1**
   ```bash
   ssh tony@stapp01
   # (password will be provided in exam portal)
   ```

2. **Create required directory**
   ```bash
   sudo mkdir -p /opt/security
   cd /opt/security
   ```

3. **Create the docker-compose file**
   ```bash
   vi docker-compose.yml
   ```

4. **Add the following content into docker-compose.yml**
   ```yaml
   version: '3.8'

   services:
     web:
       container_name: php_host
       image: php:apache
       ports:
         - "3000:80"
       volumes:
         - /var/www/html:/var/www/html

     db:
       container_name: mysql_host
       image: mariadb:latest
       ports:
         - "3306:3306"
       volumes:
         - /var/lib/mysql:/var/lib/mysql
       environment:
         MYSQL_DATABASE: database_host
         MYSQL_USER: app_user
         MYSQL_PASSWORD: ComplexP@ssw0rd!
         MYSQL_ROOT_PASSWORD: RootP@ssw0rd123
   ```

   > ✅ Key fixes:  
   > - Used `FROM php:apache` (any apache tag).  
   > - Used `mariadb:latest`.  
   > - Custom non-root user `app_user`.  
   > - Complex password set.  

5. **Save and exit**  
   - Press `ESC`, then type `:wq` and hit Enter.

6. **Start the stack**
   ```bash
   docker-compose up -d
   ```

7. **Verify running containers**
   ```bash
   docker ps
   ```

8. **Test the app**
   ```bash
   curl localhost:3000/
   ```

---

✅ At this point, the Docker Compose stack will be running with both services (`php_host` and `mysql_host`) configured correctly.
