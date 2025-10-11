# Day 63 - Challenge 
# 🧩 TASK: Deploy Iron Gallery and Iron DB in Kubernetes

# ✅ Step 1: Create the Namespace
kubectl create namespace iron-namespace-xfusion


# ✅ Step 2: Create Deployment for Iron Gallery
# Save below YAML as iron-gallery-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-gallery-deployment-xfusion
  namespace: iron-namespace-xfusion
spec:
  replicas: 1
  selector:
    matchLabels:
      run: iron-gallery
  template:
    metadata:
      labels:
        run: iron-gallery
    spec:
      containers:
        - name: iron-gallery-container-xfusion
          image: kodekloud/irongallery:2.0
          resources:
            limits:
              memory: "100Mi"
              cpu: "50m"
          volumeMounts:
            - name: config
              mountPath: /usr/share/nginx/html/data
            - name: images
              mountPath: /usr/share/nginx/html/uploads
      volumes:
        - name: config
          emptyDir: {}
        - name: images
          emptyDir: {}

# Apply it:
kubectl apply -f iron-gallery-deployment.yaml


# ✅ Step 3: Create Deployment for Iron DB
# Save below YAML as iron-db-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-db-deployment-xfusion
  namespace: iron-namespace-xfusion
spec:
  replicas: 1
  selector:
    matchLabels:
      db: mariadb
  template:
    metadata:
      labels:
        db: mariadb
    spec:
      containers:
        - name: iron-db-container-xfusion
          image: kodekloud/irondb:2.0
          env:
            - name: MYSQL_DATABASE
              value: database_web
            - name: MYSQL_ROOT_PASSWORD
              value: RootPass@123
            - name: MYSQL_PASSWORD
              value: DbPass@123
            - name: MYSQL_USER
              value: ironuser
          volumeMounts:
            - name: db
              mountPath: /var/lib/mysql
      volumes:
        - name: db
          emptyDir: {}

# Apply it:
kubectl apply -f iron-db-deployment.yaml


# ✅ Step 4: Create Service for Iron DB
# Save below YAML as iron-db-service.yaml

apiVersion: v1
kind: Service
metadata:
  name: iron-db-service-xfusion
  namespace: iron-namespace-xfusion
spec:
  selector:
    db: mariadb
  type: ClusterIP
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306

# Apply it:
kubectl apply -f iron-db-service.yaml


# ✅ Step 5: Create Service for Iron Gallery
# Save below YAML as iron-gallery-service.yaml

apiVersion: v1
kind: Service
metadata:
  name: iron-gallery-service-xfusion
  namespace: iron-namespace-xfusion
spec:
  selector:
    run: iron-gallery
  type: NodePort
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 32678

# Apply it:
kubectl apply -f iron-gallery-service.yaml


# ✅ Step 6: Verify Everything
kubectl get all -n iron-namespace-xfusion

# You should see both deployments, pods, and services running.
# The gallery app will be accessible at NodeIP:32678 (check node IP using kubectl get nodes -o wide)

