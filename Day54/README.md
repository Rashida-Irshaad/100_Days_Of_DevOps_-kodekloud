# Day 54 - Challenge 
# Share Volume Between Multiple Containers in a Pod (Kubernetes)

## Task Description

We are working on an application that will be deployed on multiple containers within a pod on a Kubernetes cluster. There is a requirement to share a volume among the containers to save some temporary data. The Nautilus DevOps team is developing a similar template to replicate the scenario.

**Requirements:**
- Create a pod named `volume-share-nautilus`.
- Use two containers:
  - **Container 1:**  
    - Image: `fedora:latest`  
    - Name: `volume-container-nautilus-1`  
    - Command: run a sleep command to keep it running  
    - Mount volume `volume-share` at `/tmp/media`
  - **Container 2:**  
    - Image: `fedora:latest`  
    - Name: `volume-container-nautilus-2`  
    - Command: run a sleep command to keep it running  
    - Mount volume `volume-share` at `/tmp/cluster`
- The volume name should be `volume-share` of type `emptyDir`.
- After creating the pod:
  - Exec into the first container and create a file `/tmp/media/media.txt` with any text content.
  - Verify that the same file appears at `/tmp/cluster/media.txt` inside the second container (shared volume).

Note: The kubectl utility on jump_host has been configured to work with the Kubernetes cluster.

---

## Solution

### Step 1: Create YAML file for the pod

```bash
vi volume-share-nautilus.yaml
```

### Step 2: Add the following configuration

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-share-nautilus
spec:
  containers:
    - name: volume-container-nautilus-1
      image: fedora:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/media
    - name: volume-container-nautilus-2
      image: fedora:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/cluster
  volumes:
    - name: volume-share
      emptyDir: {}
```

### Step 3: Apply the YAML file

```bash
kubectl apply -f volume-share-nautilus.yaml
```

### Step 4: Check the pod status

```bash
kubectl get pods
```

Ensure it shows `Running`.

### Step 5: Exec into the first container and create a file

```bash
kubectl exec -it volume-share-nautilus -c volume-container-nautilus-1 -- bash
echo "Shared volume test file" > /tmp/media/media.txt
exit
```

### Step 6: Verify from the second container

```bash
kubectl exec -it volume-share-nautilus -c volume-container-nautilus-2 -- bash
ls /tmp/cluster
cat /tmp/cluster/media.txt
exit
```

You should see the file `media.txt` and its content, confirming both containers share the same volume.

✅ **Verification Passed:** Both containers share the same `emptyDir` volume successfully.

