# Day 51 - Challenge 
# Task Description:
The Nautilus application development team has released an updated nginx image (nginx:1.17) 
that needs to be deployed on the Kubernetes cluster. 
Currently, the nginx-deployment is running nginx:1.16. 
Perform a rolling update to update the deployment to use nginx:1.17, 
and ensure all pods are up and running after the update.

# Solution:

# 1. Verify current image version
kubectl describe deployment nginx-deployment | grep Image

# 2. Find the actual container name used in this deployment
kubectl get deployment nginx-deployment -o yaml | grep name:


# 3. Perform the rolling update (replace <container-name> with the real container name)
kubectl set image deployment/nginx-deployment <container-name>=nginx:1.17

# Example (if container name is nginx-container):
 kubectl set image deployment/nginx-deployment nginx-container=nginx:1.17

# 4. Check rollout status to confirm update is progressing successfully
kubectl rollout status deployment/nginx-deployment

# 5. Verify that pods are running with the new image

kubectl describe deployment nginx-deployment | grep Image

