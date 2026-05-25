# Setup KIND and kubectl on Ubuntu Linux

---

## Step 0: System Update and Install Required Tools

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release conntrack net-tools git wget jq
```

---

## Step 1: Install Docker

```bash
sudo apt-get remove -y docker docker-engine docker.io containerd runc

sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

docker --version
docker run --rm hello-world
```

---

## Step 2: Docker Permissions

```bash
sudo usermod -aG docker $USER
newgrp docker

docker ps
docker run --rm hello-world
```

---

## Step 3: Configure Docker

```bash
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

sudo systemctl enable docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```

---

## Step 4: Networking Configuration

```bash
echo 'br_netfilter' | sudo tee /etc/modules-load.d/br_netfilter.conf
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system

sysctl net.ipv4.ip_forward
sysctl net.bridge.bridge-nf-call-iptables
```

---

## Step 5: Disable Swap

```bash
sudo swapoff -a
sudo sed -i '/\sswap\s/s/^/#/' /etc/fstab

free -h | grep Swap
```

---

## Step 6: Install KIND

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.31.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

kind --version
```

---

## Step 7: Install kubectl

```bash
LATEST=$(curl -L -s https://dl.k8s.io/release/stable.txt)

curl -LO "https://dl.k8s.io/release/${LATEST}/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/release/${LATEST}/bin/linux/amd64/kubectl.sha256"

echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

chmod +x kubectl
sudo mv kubectl /usr/local/bin/kubectl
rm kubectl.sha256

kubectl version --client
kubectl version --client --output=json
```

---

## Step 8: kubectl Autocompletion

```bash
sudo apt-get install -y bash-completion

kubectl completion bash | sudo tee /etc/bash_completion.d/kubectl > /dev/null
echo 'source <(kubectl completion bash)' >> ~/.bashrc
echo 'alias k=kubectl' >> ~/.bashrc

source ~/.bashrc
```

---

## Step 9: Verification

```bash
docker --version
docker run --rm hello-world

kind --version

kubectl version --client

echo "CPU cores: $(nproc)"
echo "Memory: $(free -h | grep Mem)"
echo "Disk: $(df -h / | tail -1)"
echo "Swap status: $(free -h | grep Swap)"

ping -c 1 8.8.8.8
curl -s -I https://kind.sigs.k8s.io/ | head -1
```

---

# cluster.yml

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

---

## Create Cluster

```bash
kind create cluster --config cluster.yml --name my-cluster
```

---

## Verify Cluster

```bash
kubectl get nodes
```

