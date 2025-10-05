# Day 56 - Challenge 
# 🧩 Task: Create Highly Available Nginx Deployment and Expose It via NodePort Service

# Description:
# Some of the Nautilus team developers are developing a static website 
# and they want to deploy it on a Kubernetes cluster. 
# The goal is to make it highly available and scalable.
#
# Therefore, we need to:
# 1. Create a deployment using nginx:latest image.
# 2. The deployment should be named nginx-deployment.
# 3. The container should be named nginx-container.
# 4. There should be 3 replicas for high availability.
# 5. Expose this deployment using a NodePort service.
# 6. The service name should be nginx-service and nodePort should be 30011.

# ✅ Solution YAML (Correct and Tested)

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx-container
          image: nginx:latest
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30011

# 🧠 Explanation:
# - Deployment ensures 3 replicas of the nginx container are always running.
# - Each container runs nginx on port 80.
# - The NodePort service exposes it externally on port 30011.
# - Requests to <Node_IP>:30011 will be forwarded to one of the nginx pods.

# 🏃 Commands to apply and verify:
# kubectl apply -f nginx-deployment.yaml
# kubectl get deployments
# kubectl get pods
# kubectl get svc

# ✅ Expected Result:
# - 3 nginx pods should be in Running state.
# - nginx-service should show type NodePort with PORT(S): 80:30011/TCP.
# - Website accessible via <Node_IP>:30011

