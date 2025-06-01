# Kubernetes Jenkins CICD Pipeline Setup

## Overview
This project aims to set up a **CI/CD pipeline** using **Jenkins** integrated with a **Kubernetes** cluster. The primary goal is to automate the building, testing, and deployment of containerized applications in a scalable, efficient, and reliable environment.

## Objectives
1. **Automate Build & Deployment Processes:** Utilize Jenkins for continuous integration and Kubernetes for continuous deployment.
2. **Scalable Application Management:** Leverage Kubernetes to deploy and scale applications automatically based on load and resource usage.
3. **Monitoring & Observability:** Implement monitoring tools like **Prometheus** and **Grafana** to gain insights into the health and performance of applications and the Kubernetes cluster.
4. **Security & Compliance:** Use tools like **kubeaudit** to scan for vulnerabilities and ensure Kubernetes cluster compliance.

---

## Phase 1: Install Jenkins & Kubernetes

### Install OpenJDK 17 JRE Headless
```bash
sudo apt install openjdk-17-jre-headless -y
```

### Download and Add Jenkins Repository
```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update
sudo apt-get install jenkins -y
```

### Setup Kubernetes Cluster using kubeadm (Version 1.28.1)

1. **Update System Packages:**
```bash
sudo apt-get update
```

2. **Install Docker:**
```bash
sudo apt install docker.io -y
sudo chmod 666 /var/run/docker.sock
```

3. **Install Required Dependencies for Kubernetes:**
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
sudo mkdir -p -m 755 /etc/apt/keyrings
```

4. **Add Kubernetes Repository and GPG Key:**
```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

5. **Install Kubernetes Components:**
```bash
sudo apt update
sudo apt install -y kubeadm=1.28.1-1.1 kubelet=1.28.1-1.1 kubectl=1.28.1-1.1
```

6. **Initialize Kubernetes Master Node:**
```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

7. **Add Worker Nodes to the Cluster:**
Replace the below command with the actual join command provided after `kubeadm init`
```bash
kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash <hash>
```

8. **Configure Kubernetes Cluster:**
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

9. **Deploy Networking Solution (Calico):**
```bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

10. **Deploy Ingress Controller (NGINX):**
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.49.0/deploy/static/provider/baremetal/deploy.yaml
```

11. **Scan Kubernetes Cluster for Issues:**
```bash
wget https://github.com/Shopify/kubeaudit/releases/download/v0.22.2/kubeaudit_0.22.2_linux_amd64.tar.gz
tar -xvzf kubeaudit_0.22.2_linux_amd64.tar.gz
sudo mv kubeaudit /usr/local/bin/
kubeaudit all
```

---

## Phase 2: Create Ubuntu EC2 Instance in AWS

1. **Sign in to AWS Management Console:**
   - Navigate to: https://aws.amazon.com/console/ and log in.

2. **Launch EC2 Instance:**
   - Choose "Ubuntu Server 24.04 LTS" AMI.
   - Select appropriate instance type (e.g., t2.micro).

3. **Configure Security Groups:**
   - Allow SSH (port 22) and other necessary ports (HTTP, HTTPS).

4. **Access Your Instance:**
   - Use SSH client (e.g., Mobaxterm) to connect to your instance.

---

## Phase 3: Install Plugins in Jenkins

1. **Eclipse Temurin Installer Plugin**  
2. **Pipeline Maven Integration Plugin**  
3. **Config File Provider Plugin**  
4. **SonarQube Scanner Plugin**  
5. **Kubernetes CLI Plugin**  
6. **Kubernetes Plugin**  
7. **Docker Plugin**  
8. **Docker Pipeline Step Plugin**  

*Configure these plugins as per your environment requirements.*

---

## Phase 4: Monitoring Setup

### Download Prometheus, Node Exporter & Blackbox Exporter
- **Prometheus & Exporters:** [https://prometheus.io/download/](https://prometheus.io/download/)
- **Blackbox Exporter:** [https://github.com/prometheus/blackbox_exporter](https://github.com/prometheus/blackbox_exporter)

### Download Grafana
- **Grafana:** [https://grafana.com/grafana/download](https://grafana.com/grafana/download)

---

## Role of Kubernetes in the Project
- **Container Orchestration:** Automate deployment, scaling, and management of containerized applications.
- **High Availability:** Distribute workloads across multiple nodes to ensure uptime.
- **Load Balancing:** Automatically distribute traffic to healthy pods.
- **Self-Healing:** Replace failed containers automatically.
- **Scalability:** Scale applications dynamically using Horizontal Pod Autoscalers (HPA).

---

## Applications That Can Be Scaled
1. **Web Applications:** React, Angular, Node.js, Django, etc.
2. **Microservices:** Independent services that can scale separately.
3. **Machine Learning Models:** Model serving APIs like TensorFlow Serving.
4. **Real-time Applications:** Chat applications, streaming services.
5. **Databases & Stateful Applications:** PostgreSQL, MongoDB (with careful configuration).
6. **Dev/Test Environments:** Spin up isolated environments for feature testing.

---

## Conclusion
This project establishes a robust CI/CD pipeline using Jenkins integrated with Kubernetes for scalable, automated deployment of applications. The setup includes monitoring tools for observability and ensures best practices for security and high availability.
