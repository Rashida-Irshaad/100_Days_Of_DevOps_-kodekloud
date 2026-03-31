# Day 59 - Challenge
# 🧾 Task: Fix Redis Deployment on Kubernetes

# 🎯 Issue Summary:
# The Redis deployment pods were stuck in "ContainerCreating" state.
# On describing the pod, two main issues were identified:
#   1️⃣ Missing ConfigMap named "redis-cofig"
#   2️⃣ Typo in the Redis image name (redis:alpin instead of redis:alpine)

# 🧩 Step-by-Step Solution

# 1️⃣ First, I checked the pod status and events to find the root cause
kubectl describe pod redis-deployment-6fd9d5fcb-4lbjd

# The describe output clearly showed:
# - ConfigMap "redis-cofig" not found
# - Image redis:alpin could not be pulled

# 2️⃣ Created the missing ConfigMap (used any simple config line)
kubectl create configmap redis-cofig --from-literal=redis.conf="bind 0.0.0.0"

# 3️⃣ Corrected the Redis image typo in the deployment
kubectl set image deployment/redis-deployment redis-container=redis:alpine

# 4️⃣ Restarted the deployment to apply the new changes
kubectl rollout restart deployment redis-deployment

# 5️⃣ Verified that new pods came up successfully
kubectl get pods -w

# ✅ Result:
# The new Redis pods started successfully and were in Running state.
# The issue was due to a missing ConfigMap and a misspelled image name.

