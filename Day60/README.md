# Day 60 - Challenge 
TASK: The Nautilus DevOps team is working on a Kubernetes template to deploy a web application on the cluster. There are some requirements to create/use persistent volumes to store the application code, and the template needs to be designed accordingly.

Create a PersistentVolume named as pv-xfusion. Configure the spec as storage class should be manual, set capacity to 5Gi, set access mode to ReadWriteOnce, volume type should be hostPath and set path to /mnt/security (this directory is already created, you might not be able to access it directly, so you need not to worry about it).

Create a PersistentVolumeClaim named as pvc-xfusion. Configure the spec as storage class should be manual, request 2Gi of the storage, set access mode to ReadWriteOnce.

Create a pod named as pod-xfusion, mount the persistent volume you created with claim name pvc-xfusion at document root of the web server, the container within the pod should be named as container-xfusion using image nginx with latest tag only (remember to mention the tag i.e nginx:latest).

Create a node port type service named web-xfusion using node port 30008 to expose the web server running within the pod.

-------------------------------------------------------------------

SOLUTION:

# Step 1: Create PersistentVolume
cat <<EOF > pv-xfusion.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-xfusion
spec:
  storageClassName: manual
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/security
EOF

kubectl apply -f pv-xfusion.yaml

# Step 2: Create PersistentVolumeClaim
cat <<EOF > pvc-xfusion.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-xfusion
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
EOF

kubectl apply -f pvc-xfusion.yaml

# Step 3: Create Pod using nginx and mount PVC
cat <<EOF > pod-xfusion.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-xfusion
  labels:
    app: nginx
spec:
  containers:
  - name: container-xfusion
    image: nginx:latest
    volumeMounts:
    - name: web-storage
      mountPath: /usr/share/nginx/html
  volumes:
  - name: web-storage
    persistentVolumeClaim:
      claimName: pvc-xfusion
EOF

kubectl apply -f pod-xfusion.yaml

# Step 4: Create NodePort Service
cat <<EOF > web-xfusion-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: web-xfusion
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
EOF

kubectl apply -f web-xfusion-service.yaml

# Step 5: Verify everything
kubectl get pv,pvc,pods,svc

