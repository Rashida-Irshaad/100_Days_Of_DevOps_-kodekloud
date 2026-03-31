# Day 64 - Challenge 
# 🧩 TASK DESCRIPTION
# One of the DevOps engineers was trying to deploy a Python Flask app on a Kubernetes cluster.
# Unfortunately, due to misconfiguration, the application was not coming up.
# You need to fix the issues so that the app becomes accessible on the specified NodePort.

# Details:
# - Deployment name: python-deployment-xfusion
# - Image to be used: poroko/flask-demo-app  (correct image name)
# - Service name: python-service-xfusion
# - NodePort should be 32345
# - TargetPort should be the Flask app’s default port (5000)
# - kubectl is already configured on the jump_host

# ✅ SOLUTION STEPS

# Step 1: Check the deployment to identify the issue
kubectl describe deployment python-deployment-xfusion

# The issue found:
# → Wrong image name: poroko/flask-app-demo (incorrect)
# → Correct image: poroko/flask-demo-app
# → Flask runs on port 5000, not 8080

# Step 2: Edit the deployment to fix the image and port
kubectl edit deployment python-deployment-xfusion

# In the editor, update under "containers":
#   image: poroko/flask-demo-app
#   ports:
#   - containerPort: 5000

# Save and exit (in vi: press ESC then type :wq)

# Step 3: Verify the deployment changes
kubectl get pods
# Wait until the pod status shows “Running”

# Step 4: Check the service configuration
kubectl describe svc python-service-xfusion

# If service ports are incorrect (e.g., port 8080 or targetPort 8080),
# fix them by editing the service:
kubectl edit svc python-service-xfusion

# In the editor, update under spec.ports:
#   ports:
#   - port: 5000
#     targetPort: 5000
#     nodePort: 32345
#     protocol: TCP
#   type: NodePort

# Save and exit (:wq)

# Step 5: Verify service configuration
kubectl get svc python-service-xfusion

# Expected output:
# NAME                      TYPE       CLUSTER-IP      PORT(S)          AGE
# python-service-xfusion     NodePort   10.x.x.x        5000:32345/TCP   5m

# Step 6: Verify application access
# Find Node IP:
kubectl get nodes -o wide

# Then open in browser or use curl:
curl http://<NodeIP>:32345

# You should see the Flask app home page.

# ✅ Final Verification:
# - Pod status: Running
# - Service port: NodePort 32345, targetPort 5000
# - App accessible via NodeIP:32345

# 🎯 TASK COMPLETED SUCCESSFULLY

