# WordPress on Kubernetes

This project deploys a **WordPress** application with a **MySQL database** on a **Minikube** cluster. Later, this setup will be migrated to **Amazon EKS**.

## 📌 Project Structure
- `mysql-secret.yaml` → Stores MySQL credentials.
- `mysql-statefulset.yaml` → Defines MySQL database using a **StatefulSet**.
- `mysql-service.yaml` → Exposes MySQL using a **ClusterIP Service**.
- `persistent-volume.yaml` → Defines a PersistentVolume for MySQL.
- `persistent-volume-claim.yaml` → Defines a PersistentVolumeClaim for MySQL.
- `wordpress-deployment.yaml` → Deploys the WordPress application.
- `wordpress-service.yaml` → Exposes WordPress using a **NodePort/LoadBalancer**.
- `ingress.yaml` → Configures an **NGINX Ingress Controller**.

## 🚀 Setup on Minikube

### 1️⃣ Install Minikube & Start Cluster
```bash
minikube start --driver=docker
```

### 2️⃣ Create a Namespace
```bash
kubectl create namespace neta-wordpress
```

### 3️⃣ Deploy MySQL
```bash
kubectl apply -f mysql-secret.yaml -n neta-wordpress
kubectl apply -f persistent-volume.yaml -n neta-wordpress
kubectl apply -f persistent-volume-claim.yaml -n neta-wordpress
kubectl apply -f mysql-statefulset.yaml -n neta-wordpress
kubectl apply -f mysql-service.yaml -n neta-wordpress
```

### 4️⃣ Deploy WordPress
```bash
kubectl apply -f wordpress-deployment.yaml -n neta-wordpress
kubectl apply -f wordpress-service.yaml -n neta-wordpress
```

### 5️⃣ Deploy Ingress Controller
```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm install nginx-ingress ingress-nginx/ingress-nginx -n neta-wordpress
```

### 6️⃣ Apply Ingress for WordPress
```bash
kubectl apply -f ingress.yaml -n neta-wordpress
```

### 7️⃣ Access WordPress
```bash
minikube service wordpress -n neta-wordpress --url
```

## 🔄 Migration to Amazon EKS
### 1️⃣ Create an EKS Cluster
Use **eksctl**:
```bash
eksctl create cluster --name eks-cluster --region us-east-1 --nodegroup-name workers --nodes 2
```

### 2️⃣ Configure kubectl for EKS
```bash
aws eks update-kubeconfig --name eks-cluster --region us-east-1
```

### 3️⃣ Deploy the Same Files on EKS
```bash
kubectl apply -f mysql-secret.yaml -n neta-wordpress
kubectl apply -f persistent-volume-claim.yaml -n neta-wordpress
kubectl apply -f mysql-statefulset.yaml -n neta-wordpress
kubectl apply -f mysql-service.yaml -n neta-wordpress
kubectl apply -f wordpress-deployment.yaml -n neta-wordpress
kubectl apply -f wordpress-service.yaml -n neta-wordpress
kubectl apply -f ingress.yaml -n neta-wordpress
```

### 4️⃣ Verify Deployment
```bash
kubectl get pods -n neta-wordpress
kubectl get svc -n neta-wordpress
kubectl get ingress -n neta-wordpress
```

## 📜 License
This project is open-source and available under the MIT License.

---

**Author:** Neta Aviv  
📌 **GitHub**: [NetaAviv](https://github.com/NetaAviv)

