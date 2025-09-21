# Day 47 - Challenge 
### Task Description
A Python app needs to be Dockerized and deployed on **App Server 2**. The `requirements.txt` file (with app dependencies) is already placed under `/python_app/src/` directory. Complete the task with the following details:

1. Create a `Dockerfile` under `/python_app` directory:
   - Use any python image as the base image.  
   - Install the dependencies using `requirements.txt`.  
   - Expose port `6400`.  
   - Run the `server.py` script using `CMD`.  

2. Build an image named `nautilus/python-app` using this Dockerfile.  

3. Create a container named `pythonapp_nautilus`:
   - Map container port `6400` to host port `8095`.  

4. Once deployed, test the app using:  
   ```bash
   curl http://localhost:8095/
   ```

---

### Solution Steps

1. **SSH into App Server 2**
   ```bash
   ssh steve@stapp02
   # (use the provided password from exam portal)
   ```

2. **Navigate to the app directory**
   ```bash
   cd /python_app
   ```

3. **Create the Dockerfile**
   ```bash
   vi Dockerfile
   ```

4. **Add the following content in Dockerfile**
   ```dockerfile
   # Use Python base image
   FROM python:3.9-slim

   # Set working directory
   WORKDIR /app

   # Copy requirements.txt
   COPY src/requirements.txt /app/

   # Install dependencies
   RUN pip install --no-cache-dir -r requirements.txt

   # Copy application files
   COPY src/ /app/

   # Expose port 6400
   EXPOSE 6400

   # Run the application
   CMD ["python", "server.py"]
   ```

5. **Save and exit**  
   Press `ESC`, then type `:wq` and hit Enter.

6. **Build the Docker image**
   ```bash
   docker build -t nautilus/python-app .
   ```

7. **Run the container**
   ```bash
   docker run -d --name pythonapp_nautilus -p 8095:6400 nautilus/python-app
   ```

8. **Verify the container is running**
   ```bash
   docker ps
   ```

9. **Test the app**
   ```bash
   curl http://localhost:8095/
   ```

---

✅ At this point, the Python app will be Dockerized, deployed in a container named `pythonapp_nautilus`, and accessible at `http://localhost:8095/`.
