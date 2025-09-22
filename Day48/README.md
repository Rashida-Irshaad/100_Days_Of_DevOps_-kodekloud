# Day 48 - Challenge 
# Task Description:
# The Nautilus DevOps team is diving into Kubernetes for application management. 
# One team member has a task to create a pod according to the details below:
#
# - Create a pod named pod-nginx using the nginx image with the latest tag. 
#   Ensure to specify the tag as nginx:latest.
# - Set the app label to nginx_app.
# - Name the container as nginx-container.
#
# Note: The kubectl utility on jump_host is configured to operate with the Kubernetes cluster.

# 1. Create the Pod manifest file
vi pod-nginx.yaml

# Paste the following YAML content inside pod-nginx.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: pod-nginx
  labels:
    app: nginx_app
spec:
  containers:
    - name: nginx-container
      image: nginx:latest

# 2. Apply the manifest to create the pod
kubectl apply -f pod-nginx.yaml

# 3. Verify the pod is running
kubectl get pods -o wide

# 4. (Optional) Check pod details
kubectl describe pod pod-nginx
