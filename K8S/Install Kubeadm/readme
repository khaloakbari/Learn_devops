# Deploy Kubernetes Cluster on Ubuntu 22.04

#### 📝 Introduction

Kubernetes is an open-source container orchestration system for automating software deployment, scaling, and management. Originally designed by Google, the project is now maintained by a worldwide community of contributors, and the trademark is held by the Cloud Native Computing Foundation.

### Installation ✔️

## 1. Disable Swap

Kubernetes schedules work based on the understanding of available resources. If workloads start using swap, it can become difficult for Kubernetes to make accurate scheduling decisions. Therefore, it’s recommended to disable swap before installing Kubernetes. Open the `/etc/fstab` file with a text editor.

#### There are two ways to disable swap:
#### First way:

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

#### Second way:
```bash
sudo vim /etc/fstab
```

Look for the line that references the swap file. It will usually look something like this:

```vim
/swapfile          none          swap          sw          0          0
```
Delete this line, then reboot the system.

#### Note: 💡
##### To allow kubelet to work properly, we need to disable swap on all nodes (Master and worker nodes).

## 2. Set up the IPv4 bridge on all nodes

To configure the IPv4 bridge on all nodes, execute the following commands on each node.
Load the `br_netfilter` module required for networking:

```bash
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
sudo modprobe overlay
sudo modprobe br_netfilter
```

To allow `iptables` to see bridged traffic, as required by Kubernetes, we need to set the values of certain fields to 1.

```bash
cat <<EOF | sudo tee /etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
EOF
```
Apply sysctl params without reboot:

```bash
sudo sysctl --system
```

## 3. Installing Containerd

```bash
sudo apt update
sudo apt install containerd -y
```

Set up the default configuration file:

```bash
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml
```

Modify the containerd configuration file and ensure that the `cgroupDriver` is set to `systemd`:

```bash
sudo vim /etc/containerd/config.toml
```

Search for `SystemdCgroup = false` and change it to `true`:

```bash
SystemdCgroup = true
```

Restart containerd to apply changes:

```bash
sudo systemctl restart containerd
sudo systemctl enable containerd
```

## 4. Install kubelet, kubeadm, and kubectl
Let’s install kubelet, kubeadm, and kubectl on each node to create a Kubernetes cluster.

#### ⚠️ These instructions are for Kubernetes v1.30. ⚠️

### 4.1. Update the apt package index and install packages needed for the Kubernetes repository:
```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
```

### 4.2. Download the public signing key for the Kubernetes package repositories:

```bash
sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

### 4.3. Add the Kubernetes `apt` repository:
```bash
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

### 4.4. Update the package index and install Kubernetes components:
```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

### 4.5. Enable the kubelet service before running kubeadm:
```bash
sudo systemctl enable --now kubelet
```

### 4.6. Pull Kubernetes images before initializing the cluster:
```bash
sudo kubeadm config images pull
```

## 5. Initialize Cluster

```bash
kubeadm init --control-plane-endpoint "<FQDN or IPAddress>:6443" --pod-network-cidr=10.244.0.0/16 --upload-certs
```

If the cluster does not work, reset it with:
```bash
sudo kubeadm reset --force
```

To manage the cluster, configure `kubectl` on the master node:
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Alternatively, if running as root:
```bash
export KUBECONFIG=/etc/kubernetes/admin.conf
```

## 6. Install Flannel

Deploy Flannel:
```bash
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

### ⚠️ Warning ⚠️
If using a custom `podCIDR` (not `10.244.0.0/16`), download and modify the manifest accordingly.

## 7. Kubectl Autocompletion

### BASH
```bash
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

#### Alias for `kubectl`:
```bash
alias k=kubectl
complete -o default -F __start_kubectl k
```

### ZSH
```bash
source <(kubectl completion zsh)
echo '[[ $commands[kubectl] ]] && source <(kubectl completion zsh)' >> ~/.zshrc
```

### FISH
```bash
echo 'kubectl completion fish | source' > ~/.config/fish/completions/kubectl.fish && source ~/.config/fish/completions/kubectl.fish
```

## 8. Join Worker Node to Cluster
Run the following command on worker nodes:
```bash
sudo kubeadm join [master-node-ip]:6443 --token [token] \
    --discovery-token-ca-cert-hash sha256:[hash]
```

