# Kubernetes Setup with kubeadm

This guide will walk you through setting up a Kubernetes environment on Ubuntu using containerd as the container runtime.

---

## 1. System Update and Upgrade

```bash
sudo apt update -y && sudo apt upgrade -y
```

---

## 2. Disable Swap (Kubernetes Requires Swap to Be Disabled)

```bash
sudo swapoff -a
sudo sed -i '/swap.img/s/^/#/' /etc/fstab
```

---

## 3. Configure Kernel Modules for containerd

Create the module configuration file:

```bash
sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF
```

Load the kernel modules:

```bash
sudo modprobe overlay
sudo modprobe br_netfilter
```

---

## 4. Configure Network Settings for Kubernetes

```bash
echo "net.bridge.bridge-nf-call-ip6tables = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf
echo "net.bridge.bridge-nf-call-iptables = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf
```

Apply the new network settings:

```bash
sudo sysctl --system
```

---

## 5. Install Required Packages and Add Docker Repository

```bash
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

---

## 6. Install containerd

```bash
sudo apt update -y
sudo apt install -y containerd.io
```

Configure containerd:

```bash
containerd config default | sudo tee /etc/containerd/config.toml > /dev/null
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
```

Restart and enable containerd:

```bash
sudo systemctl restart containerd
sudo systemctl enable containerd
```

---

## 7. Add Kubernetes Repository

```bash
sudo mkdir -p /etc/apt/keyrings
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

---

## 8. Install Kubernetes Components

```bash
sudo apt update -y
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

---

## 9. Install Kubernetes Components
```bash
sudo kubeadm init
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

## 10. Install Calico Networking
```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```

---

## 11. Join workers
```bash
sudo kubeadm join your_master_ip:6443 --token your_token --discovery-token-ca-cert-hash your_sha
```

---

## Done!

You are now ready to initialize your Kubernetes cluster using `kubeadm` and join worker nodes.
