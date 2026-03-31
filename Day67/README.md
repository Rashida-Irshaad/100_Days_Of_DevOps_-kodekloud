# Day 67 - Challenge 
### 🧾 Task Description

The Nautilus Application Development team has completed a **Guestbook application** to manage visitor entries. The infrastructure needs to be deployed on a Kubernetes cluster with **three tiers**: Redis Master, Redis Slave, and Frontend.

---

### 🎯 Requirements
1. **Redis Master Deployment & Service**
   - Deployment name: `redis-master`
   - Container: `master-redis-devops`
   - Image: `redis`
   - Replicas: `1`
   - Resources: `CPU=100m`, `Memory=100Mi`
   - Port: `6379`

2. **Redis Slave Deployment & Service**
   - Deployment name: `redis-slave`
   - Container: `slave-redis-devops`
   - Image: `gcr.io/google_samples/gb-redisslave:v3`
   - Replicas: `2`
   - Environment Variable: `GET_HOSTS_FROM=dns`
   - Resources: `CPU=100m`, `Memory=100Mi`
   - Port: `6379`

3. **Frontend Deployment & Service**
   - Deployment name: `frontend`
   - Container: `php-redis-devops`
   - Image: `gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff`
   - Replicas: `3`
   - Environment Variable: `GET_HOSTS_FROM=dns`
   - Resources: `CPU=100m`, `Memory=100Mi`
   - Port: `80`
   - Service Type: `NodePort`
   - NodePort: `30009`

💡 *Note:* You can use any labels you prefer.

---

### ⚙️ Solution (All Steps in One Block)

```bash
# Step 1: Redis Master Deployment
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-master
  template:
    metadata:
      labels:
        app: redis-master
    spec:
      containers:
      - name: master-redis-devops
        image: redis
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
        ports:
        - containerPort: 6379
---
# Step 2: Redis Master Service
apiVersion: v1
kind: Service
metadata:
  name: redis-master
spec:
  selector:
    app: redis-master
  ports:
  - port: 6379
    targetPort: 6379
---
# Step 3: Redis Slave Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis-slave
  template:
    metadata:
      labels:
        app: redis-slave
    spec:
      containers:
      - name: slave-redis-devops
        image: gcr.io/google_samples/gb-redisslave:v3
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
        env:
        - name: GET_HOSTS_FROM
          value: dns
        ports:
        - containerPort: 6379
---
# Step 4: Redis Slave Service
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
spec:
  selector:
    app: redis-slave
  ports:
  - port: 6379
    targetPort: 6379
---
# Step 5: Frontend Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: php-redis-devops
        image: gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
        env:
        - name: GET_HOSTS_FROM
          value: dns
        ports:
        - containerPort: 80
---
# Step 6: Frontend Service
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  type: NodePort
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30009
EOF

# Step 7: Verify everything
kubectl get all
