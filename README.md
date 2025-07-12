# Kubernetes
### 🚀 MicroK8s on EC2 - One Page Notes

---

#### ✅ EC2 Instance Setup

* **OS**: Ubuntu Server 22.04 LTS (64-bit x86)
* **Instance**: 2 CPUs, 4GB RAM
* **Security Group Rules**:

  * Port 22: SSH access
  * Port 16443: Kubernetes Dashboard access
  * Ports 30000–32767: NodePort services (optional but useful)

---

#### 🔐 Connect to EC2

```bash
ssh -i <your-key.pem> ubuntu@<EC2_PUBLIC_IP>
```

---

#### ⚙️ Install & Configure MicroK8s

```bash
sudo apt update && sudo apt upgrade -y           # System update
sudo snap install microk8s --classic              # Install MicroK8s

sudo usermod -a -G microk8s $USER                 # Add user to microk8s group
sudo chown -f -R $USER ~/.kube                    # Fix kubeconfig permissions
newgrp microk8s                                   # Refresh group membership
```

---

#### 🚦 Enable Core Add-ons

```bash
microk8s status --wait-ready                      # Wait until ready
microk8s enable dns dashboard ingress storage     # Enable essential services
```

---

#### 🌍 Deploy an NGINX App

```bash
microk8s kubectl create deployment nginx --image=nginx     # Create deployment
microk8s kubectl expose deployment nginx --port=80 --type=NodePort  # Expose it
microk8s kubectl get svc                                   # Get external port
```

> Access: `http://<EC2_PUBLIC_IP>:<NodePort>` (e.g. 30777)

---

#### 📊 Access Kubernetes Dashboard

```bash
microk8s dashboard-proxy                       # Launch local proxy
```

> Open: `https://<EC2_PUBLIC_IP>:16443`

---

#### 🔑 Get Dashboard Token

```bash
microk8s kubectl -n kube-system get secret                 # List secrets
microk8s kubectl -n kube-system describe secret <token-name>  # Copy token
```

---

#### 🛠 Fix Dashboard Port Conflict

```bash
sudo lsof -i :10443                      # Check if port is in use
sudo kill -9 <PID>                       # Kill conflicting process
microk8s dashboard-proxy --address='0.0.0.0'   # Bind to all interfaces
```

---

#### 📋 Other Useful Commands

```bash
microk8s kubectl get svc whoami-service     # View custom service details
microk8s kubectl get pods -o wide           # Detailed pod info across nodes
```

---

#### 📌 Next Steps

* 👤 Create secure access via RBAC
* 🌐 Use Ingress + TLS for domains
* 📈 Monitor with Prometheus + Grafana
* ⚙️ Auto-deploy with CI/CD (GitHub Actions, ArgoCD)
