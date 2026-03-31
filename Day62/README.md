# Day 62 - Challenge 
TASK DESCRIPTION:
-----------------
The Nautilus DevOps team wants to deploy some tools in the Kubernetes cluster. 
Some tools are license-based, so their license information must be stored securely inside the cluster.
For that, we’ll use Kubernetes Secrets.

Requirements:
1. A secret key file `beta.txt` already exists under `/opt` on the jump host.
2. Create a generic secret named `beta` using that file (it contains the license number/password).
3. Create a Pod named `secret-datacenter`.
4. The Pod should:
   - Have one container named `secret-container-datacenter`.
   - Use the image `fedora:latest`.
   - Run a `sleep` command so it stays running.
   - Mount the secret we created (`beta`) inside the container at `/opt/apps`.
5. Verify the secret by exec’ing into the container and checking the contents under `/opt/apps`.

---------------------------------------------------------------------
SOLUTION:
---------------------------------------------------------------------

# Step 1: Go to the /opt directory and verify the secret file
cd /opt
cat beta.txt
# (This will show the license/password content — we don’t change it)

# Step 2: Create the Kubernetes secret named "beta" using the file beta.txt
kubectl create secret generic beta --from-file=/opt/beta.txt

# Step 3: Create a Pod configuration file for secret-datacenter
cat <<EOF > secret-datacenter.yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-datacenter
spec:
  containers:
  - name: secret-container-datacenter
    image: fedora:latest
    command: [ "sleep", "3600" ]
    volumeMounts:
    - name: secret-volume
      mountPath: /opt/apps
  volumes:
  - name: secret-volume
    secret:
      secretName: beta
EOF

# Step 4: Apply the Pod configuration
kubectl apply -f secret-datacenter.yaml

# Step 5: Verify that the Pod is running
kubectl get pods

# Step 6: Exec into the container to confirm the secret is mounted
kubectl exec -it secret-datacenter -- /bin/bash

# Inside the container, check the secret file
ls /opt/apps
cat /opt/apps/beta.txt
# You should see the same content as in the original /opt/beta.txt file.

# Step 7: Exit the container
exit

---------------------------------------------------------------------
EXPLANATION:
---------------------------------------------------------------------
- We used **Kubernetes Secrets** to store sensitive data (like license keys) securely.
- The `kubectl create secret generic` command creates a secret object from a file.
- The **Pod** mounts that secret inside the container filesystem (at `/opt/apps`).
- The `sleep 3600` command keeps the container alive for testing.
- Finally, we checked inside the container that the secret file exists and contains the correct value.

