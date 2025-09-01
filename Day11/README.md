
# Day 11 - Challenge
# Day 11 - Install and Configure Tomcat on App Server 2

## Task Description
The Nautilus application development team recently finished the beta version of a Java-based application, which they plan to deploy on App Server 2 within the **Stratos Datacenter** using **Tomcat**.  

Your task is to:

- Install Tomcat server on App Server 2.  
- Configure Tomcat to run on port **6200**.  
- Deploy the `ROOT.war` file located on the **Jump host** at `/tmp` to this Tomcat server.  
- Verify that the webpage works directly on the base URL using `curl`.

---

## Steps to Complete Task
1. **Login to App Server 2**:
    ```bash
    ssh steve@stapp02
    ```

2. **Install Tomcat Server**:
    ```bash
    sudo yum install -y tomcat
    ```

3. **Configure Tomcat to Run on Port 6200**:
    ```bash
    sudo vi /etc/tomcat/server.xml
    ```
    - Find the line containing the connector port (default is 8080):
    ```xml
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    ```
    - Change the port to 6200:
    ```xml
    <Connector port="6200" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    ```
    - Save and exit (`Esc`, `:wq`, `Enter`).

4. **Start and Enable Tomcat Service**:
    ```bash
    sudo systemctl enable tomcat
    sudo systemctl start tomcat
    ```

5. **Copy `ROOT.war` from Jump Host to App Server 2**:
    ```bash
    scp tony@jump_host:/tmp/ROOT.war /tmp/
    sudo mv /tmp/ROOT.war /var/lib/tomcat/webapps/
    ```

6. **Verify Tomcat Deployment**:
    ```bash
    curl http://stapp02:6200
    ```
    - You should see the deployed application’s homepage content.

7. **Optional: Check Tomcat Logs**:
    ```bash
    sudo tail -f /var/log/tomcat/catalina.out
    ```

---

**✅ Done**  
Tomcat is installed, running on port **6200**, `ROOT.war` is deployed, and the application is accessible from the base URL.

