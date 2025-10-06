# Day 61 - Challenge The Nautilus DevOps team wants to test Init Containers in Kubernetes. Create a Deployment named ic-deploy-nautilus with 1 replica. Both selector and template labels should be app=ic-nautilus. The init container named ic-msg-nautilus should use image debian:latest and run command /bin/bash -c "echo Init Done - Welcome to xFusionCorp Industries > /ic/blog". Mount a volume named ic-volume-nautilus at /ic. The main container named ic-main-nautilus should also use image debian:latest and run command /bin/bash -c "while true; do cat /ic/blog; sleep 5; done". Mount the same volume at /ic. The volume should be of type emptyDir.
# Solution:-

vi ic-deploy-nautilus.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ic-deploy-nautilus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ic-nautilus
  template:
    metadata:
      labels:
        app: ic-nautilus
    spec:
      initContainers:
      - name: ic-msg-nautilus
        image: debian:latest
        command: ["/bin/bash", "-c", "echo Init Done - Welcome to xFusionCorp Industries > /ic/blog"]
        volumeMounts:
        - name: ic-volume-nautilus
          mountPath: /ic
      containers:
      - name: ic-main-nautilus
        image: debian:latest
        command: ["/bin/bash", "-c", "while true; do cat /ic/blog; sleep 5; done"]
        volumeMounts:
        - name: ic-volume-nautilus
          mountPath: /ic
      volumes:
      - name: ic-volume-nautilus
        emptyDir: {}

kubectl apply -f ic-deploy-nautilus.yaml
kubectl get pods

kubectl logs -f $POD


