# Day 52 - Challenge 
# Task Description:
The Nautilus DevOps team deployed a new release of an application, 
but a bug was reported by a customer. 
There is an existing deployment named nginx-deployment, 
and we need to roll back this deployment to its previous version.

# Solution:

# 1. Check the rollout history to see available revisions
kubectl rollout history deployment/nginx-deployment

# 2. Roll back the deployment to the previous revision
kubectl rollout undo deployment/nginx-deployment

# 3. (Optional) If you want to roll back to a specific revision, run:
# kubectl rollout undo deployment/nginx-deployment --to-revision=<revision_number>

# 4. Check the status of the rollback
kubectl rollout status deployment/nginx-deployment

# 5. Verify the pods are running with the previous version
kubectl describe deployment nginx-deployment | grep Image
kubectl get pods -o wide

