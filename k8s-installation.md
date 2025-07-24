# ☸️ Kubernetes Cluster Setup on Ubuntu 24.04 (Using kubeadm, containerd, and Calico)

This guide provides step-by-step instructions to configure a multi-node Kubernetes cluster using `kubeadm` and `containerd` as the container runtime.

---

## 📍 1. Set Hostname & Update Hosts File

Run the following commands **on each node** with appropriate hostname:

```bash
# Master Node
sudo hostnamectl set-hostname "k8s-master-node"

# Worker Node 1
sudo hostnamectl set-hostname "k8s-slave-1"

# Worker Node 2
sudo hostnamectl set-hostname "k8s-slave-2"
````

Update the `/etc/hosts` file **on all nodes** with the following content:

```bash
<master  privete_ip> k8s-master-node
<worker1 privete_ip> k8s-slave-1
<worker2 privete_ip> k8s-slave-2
```

---

## 🚫 2. Disable Swap & Load Kernel Modules

Disable swap memory and configure kernel modules like overlay and br_netfilter for Kubernetes:

```bash
sudo swapoff -a && sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

sudo modprobe overlay && sudo modprobe br_netfilter
```

Enable kernel modules on boot:

```bash
echo -e "overlay\nbr_netfilter" | sudo tee /etc/modules-load.d/k8s.conf
```

Configure sysctl parameters for Kubernetes:
Add the kernel parameters like IP forwarding. Create a file and load the parameters using sysctl command -

```bash
cat <<EOF | sudo tee /etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

# To load the above kernel parameters, run
sudo sysctl --system
```

---

## 📦 3. Install and Configure Containerd

Install containerd with SystemdCgroup support:

```bash
sudo apt update
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates

# Add Docker GPG key
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker.gpg

# Add Docker repository
sudo add-apt-repository \
  "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# Install containerd
sudo apt update && sudo apt install containerd.io -y

# Generate and configure default containerd config
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml > /dev/null

# Set SystemdCgroup = true
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

# Enable and restart service
sudo systemctl restart containerd
sudo systemctl enable containerd
```

---

## 📚 4. Add Kubernetes APT Repository

Always check for the latest release here- https://kubernetes.io/releases/. We should install lts-1 version to avoid any unresolved issues. Here we've used 1.32.

```bash
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/k8s.gpg

echo 'deb [signed-by=/etc/apt/keyrings/k8s.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/k8s.list

```

---

## ⚙️ 5. Install Kubernetes Components

Install `kubelet`, `kubeadm`, and `kubectl`:

```bash
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
```

---

## 🧠 6. Initialize Kubernetes Cluster (Control Plane)

On the **master node**:

```bash
sudo kubeadm init --control-plane-endpoint=k8s-master-node
```

Configure kubectl access for the current user:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

## 🧩 7. Join Worker Nodes

On each **worker node**, join the cluster using the token provided during `kubeadm init`. Example:

```bash
sudo kubeadm join k8s-master-node:6443 --token <TOKEN> \
  --discovery-token-ca-cert-hash sha256:<HASH>
```

Verify node registration from the **master node**:

```bash
kubectl get nodes
```

---

## 🌐 8. Install Calico CNI (Network Plugin)

On the **master node**, apply Calico manifest (always check for latest calico.yaml):

```bash
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.30.2/manifests/calico.yaml
```

Wait a few moments and verify:

```bash
kubectl get pods -n kube-system
kubectl get nodes
```

---

## 🚀 9. Test Kubernetes Cluster Setup

Create a test deployment and expose it via NodePort:

```bash
# Create namespace and deployment
kubectl create ns demo-app
kubectl create deployment nginx-app --image nginx --replicas 2 --namespace demo-app

# Verify resources
kubectl get deployment -n demo-app
kubectl get pods -n demo-app

# Expose the deployment
kubectl expose deployment nginx-app -n demo-app --type NodePort --port 80

# Get the service info
kubectl get svc -n demo-app
```

Access the application from a browser or CLI:

```bash
curl http://<Any-Worker-IP>:<NodePort>
```
