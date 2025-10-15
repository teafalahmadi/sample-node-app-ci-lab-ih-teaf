# 🚀 Node.js App on Azure Kubernetes Service (AKS)

This project demonstrates deploying a simple Node.js app to Azure Kubernetes Service (AKS).  
The app is containerized, configured through a ConfigMap, and exposed both internally and publicly.

---

## 🧩 Kubernetes Files Overview

| File | Purpose |
|------|----------|
| `ns.yaml` | Creates the namespace **node-challenge** to isolate all resources. |
| `config.yaml` | Stores environment variables like **BANNER** used by the app. |
| `deploy.yaml` | Runs the Node.js app (3 replicas) using the image from Docker Hub. |
| `svc-clusterip.yaml` | Exposes the app internally (ClusterIP). |
| `svc-loadbalancer.yaml` | Exposes the app publicly (LoadBalancer). |

---

## ⚙️ Deployment Steps
```bash
# 1️⃣ Build & Push Image
docker build -t docker.io/teaf/node-app:v1.0.0 .
docker push docker.io/teaf/node-app:v1.0.0
```

---

## 2️⃣ Apply Kubernetes Manifests
kubectl apply -f ns.yaml
kubectl apply -f config.yaml
kubectl apply -f deploy.yaml
kubectl apply -f svc-clusterip.yaml
kubectl apply -f svc-loadbalancer.yaml

## 3️⃣ Verify Deployment
kubectl -n node-challenge get all

# ✅ You should see:
# - 3 Pods running
# - node-internal (ClusterIP)
# - node-public (LoadBalancer with EXTERNAL-IP)

## 4️⃣ Test the App
# 🌐 External Access
curl -I http://<EXTERNAL-IP>/health

## 🔒 Internal Access
kubectl -n node-challenge run toolbox --image=busybox:1.36 -it -- sh
wget -qO- http://node-internal.node-challenge.svc.cluster.local:3000/health

# 5️⃣ Cleanup
kubectl delete ns node-challenge
