# Day 57 - Challenge 
Task: Create a Pod to Print Environment Variables as a Greeting

Description:
- Create a pod named print-envars-greeting.
- The container should be named print-env-container and use the bash image.
- Add three environment variables:
  a. GREETING = Welcome to  
  b. COMPANY = DevOps  
  c. GROUP = Datacenter
- Use this command:
  ["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"']
- Set restartPolicy to Never to prevent crash loops.
- Verify output using: kubectl logs -f print-envars-greeting

Solution:

apiVersion: v1
kind: Pod
metadata:
  name: print-envars-greeting
spec:
  containers:
    - name: print-env-container
      image: bash
      command: ["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"']
      env:
        - name: GREETING
          value: "Welcome to"
        - name: COMPANY
          value: "DevOps"
        - name: GROUP
          value: "Datacenter"
  restartPolicy: Never

Commands to Apply and Verify:
kubectl apply -f print-envars-greeting.yaml
kubectl get pods
kubectl logs -f print-envars-greeting

Expected Output:
Welcome to DevOps Datacenter
