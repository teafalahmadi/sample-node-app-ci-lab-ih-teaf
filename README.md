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

### 1️⃣ Build & Push Image
```bash
docker build -t docker.io/teaf/node-app:v1.0.0 .
docker push docker.io/teaf/node-app:v1.0.0

2️⃣ Apply Manifests
kubectl apply -f ns.yaml
kubectl apply -f config.yaml
kubectl apply -f deploy.yaml
kubectl apply -f svc-clusterip.yaml
kubectl apply -f svc-loadbalancer.yaml

3️⃣ Verify
kubectl -n node-challenge get all


You should see:

3 pods running

node-internal (ClusterIP)

node-public (LoadBalancer with EXTERNAL-IP)

🧪 Testing the App
External Access
curl -I http://<EXTERNAL-IP>/health

Internal Access
kubectl -n node-challenge run toolbox --image=busybox:1.36 -it -- sh
wget -qO- http://node-internal.node-challenge.svc.cluster.local:3000/health

🧹 Cleanup
kubectl delete ns node-challenge

🧠 Key Points

ConfigMap provides environment variables without changing code.

Deployment manages scaling and rolling updates.

ClusterIP = internal access only.

LoadBalancer = public internet access.

Everything is reproducible with simple kubectl apply commands.
