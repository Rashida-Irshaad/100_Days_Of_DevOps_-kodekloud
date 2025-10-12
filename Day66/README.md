# Day 66 - Challenge 
### 🧩 Task Description:
The Nautilus DevOps team needs to deploy a MySQL database on the Kubernetes cluster using persistent storage and secrets for secure credentials.  
This setup ensures that MySQL data is not lost if the pod restarts and that passwords are stored safely using Kubernetes secrets.

---

### ✅ Solution Steps:

# Step 1: Create PersistentVolume (PV)
cat <<EOF > mysql-pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 250Mi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: /mnt/mysql-data
EOF
kubectl apply -f mysql-pv.yaml

# Step 2: Create PersistentVolumeClaim (PVC)
cat <<EOF > mysql-pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  resources:
    requests:
      storage: 250Mi
EOF
kubectl apply -f mysql-pvc.yaml

# Step 3: Create Secrets for MySQL credentials
kubectl create secret generic mysql-root-pass --from-literal=password=YUIidhb667
kubectl create secret generic mysql-user-pass --from-literal=username=kodekloud_aim --from-literal=password=ksH85UJjhb
kubectl create secret generic mysql-db-url --from-literal=database=kodekloud_db8

# Step 4: Create MySQL Deployment
cat <<EOF > mysql-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql-container
        image: mysql:latest
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-root-pass
              key: password
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: mysql-db-url
              key: database
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: username
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: password
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
EOF
kubectl apply -f mysql-deployment.yaml

# Step 5: Create NodePort Service for MySQL
cat <<EOF > mysql-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  type: NodePort
  selector:
    app: mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
      nodePort: 30007
EOF
kubectl apply -f mysql-service.yaml

# Step 6: Verify everything
kubectl get pv
kubectl get pvc
kubectl get secrets
kubectl get deploy
kubectl get pods
kubectl get svc

