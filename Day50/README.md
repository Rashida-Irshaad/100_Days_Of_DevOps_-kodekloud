# Day 50 - Challenge 
# Task Description:
# The Nautilus DevOps team has noticed performance issues in some Kubernetes-hosted applications due to resource constraints. 
# To fix this, they want to create a pod with resource requests and limits.
# 
# Requirements:
# - Pod name: httpd-pod
# - Container name: httpd-container
# - Image: httpd:latest
# - Resource Requests: 15Mi memory, 100m CPU
# - Resource Limits: 20Mi memory, 100m CPU
vi httpd-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
    - name: httpd-container
      image: httpd:latest
      resources:
        requests:
          memory: "15Mi"
          cpu: "100m"
        limits:
          memory: "20Mi"
          cpu: "100m"Apply this file:
# - Apply this file:
kubectl apply -f httpd-pod.yaml

