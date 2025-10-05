# Day 58 - Challenge
Task: Deploy Grafana on Kubernetes Cluster

Description:
1. Create a deployment named grafana-deployment-nautilus using a Grafana image.
2. Expose it using a NodePort type service with nodePort 32000.
3. You only need to ensure the Grafana login page is accessible—no internal setup required.

Solution:

# Step 1: Create Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment-nautilus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
        - name: grafana-container
          image: grafana/grafana:latest
          ports:
            - containerPort: 3000

---
# Step 2: Create Service
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
spec:
  type: NodePort
  selector:
    app: grafana
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 32000

Commands to Apply and Verify:
kubectl apply -f grafana-deployment.yaml
kubectl get pods
kubectl get svc

Access Grafana:
Use the Node’s IP and NodePort in your browser:
http://<Node_IP>:32000

Expected Result:
Grafana login page should load successfully.
 
