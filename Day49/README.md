# Day 49 - Challenge 
# Task Description:
# The Nautilus DevOps team is delving into Kubernetes for app management. 
# One team member needs to create a deployment following these details:
# - Create a deployment named httpd
# - Deploy the application httpd using the image httpd:latest (ensure to specify the tag)
# - Note: The kubectl utility on jump_host is set up to interact with the Kubernetes cluster.

# Step 1: Create a YAML file for the deployment
vi httpd-deployment.yaml

# Step 2: Add the following content inside the YAML file
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpd
  template:
    metadata:
      labels:
        app: httpd
    spec:
      containers:
        - name: httpd-container
          image: httpd:latest

# Step 3: Apply the deployment
kubectl apply -f httpd-deployment.yaml

# Step 4: Verify the deployment and pod
kubectl get deployments
kubectl get pods

