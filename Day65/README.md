# Day 65 - Challenge 
# 🧩 TASK DESCRIPTION
# The Nautilus DevOps team needs to deploy Redis for testing on a Kubernetes cluster.
# They decided to use a config map to define memory limits for Redis and mount necessary volumes.
# Your task is to create a Redis deployment with all given requirements.

# ✅ REQUIREMENTS
# - Create a ConfigMap named: my-redis-config
#   → It should contain: maxmemory 2mb  (under redis-config key)
# - Deployment name: redis-deployment
# - Container name: redis-container
# - Image: redis:alpine
# - Replicas: 1
# - Container port: 6379
# - Resource request: 1 CPU
# - Mount volumes:
#   a) EmptyDir volume named data → mountPath: /redis-master-data
#   b) ConfigMap volume named redis-config → mountPath: /redis-master
# - Deployment must be running successfully.

# ✅ SOLUTION STEPS

# Step 1: Create the ConfigMap
kubectl create configmap my-redis-config \
  --from-literal=redis-config="maxmemory 2mb"

# Step 2: Verify ConfigMap creation
kubectl get configmap my-redis-config -o yaml

# Step 3: Create the Redis deployment YAML file
cat <<EOF > redis-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis-container
        image: redis:alpine
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: "1"
        volumeMounts:
        - name: data
          mountPath: /redis-master-data
        - name: redis-config
          mountPath: /redis-master
      volumes:
      - name: data
        emptyDir: {}
      - name: redis-config
        configMap:
          name: my-redis-config
EOF

# Step 4: Apply the deployment
kubectl apply -f redis-deployment.yaml

# Step 5: Verify the deployment and pods
kubectl get deployments
kubectl get pods

# Wait until the pod STATUS is "Running"

# Step 6: (Optional) Check inside the Redis container
kubectl exec -it $(kubectl get pod -l app=redis -o jsonpath="{.items[0].metadata.name}") -- sh

# Inside the container, verify the config file mounted:
ls /redis-master
cat /redis-master/redis-config

# You should see:
# maxmemory 2mb

# ✅ VERIFICATION CHECKLIST
# - ConfigMap “my-redis-config” created ✔️
# - Deployment “redis-deployment” created ✔️
# - Container “redis-container” running redis:alpine ✔️
# - Volume “data” mounted at /redis-master-data ✔️
# - ConfigMap volume “redis-config” mounted at /redis-master ✔️
# - Redis listening on port 6379 ✔️
# - Pod status = Running ✔️

# 🎯 TASK COMPLETED SUCCESSFULLY

