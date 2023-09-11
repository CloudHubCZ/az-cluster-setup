## Pass
user: cloudhub
pass: Kubernetes123

## IPTables
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system

## Container
wget https://github.com/containerd/containerd/releases/download/v1.6.2/containerd-1.6.2-linux-amd64.tar.gz
sudo tar Czxvf /usr/local containerd-1.6.2-linux-amd64.tar.gz
wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service
sudo mv containerd.service /usr/lib/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now containerd
sudo systemctl status containerd
wget https://github.com/opencontainers/runc/releases/download/v1.1.1/runc.amd64
sudo install -m 755 runc.amd64 /usr/local/sbin/runc
sudo mkdir -p /etc/containerd/
containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
sudo systemctl restart containerd

## Kubectl/adm
https://computingforgeeks.com/deploy-kubernetes-cluster-on-ubuntu-with-kubeadm/

sudo apt -y install curl apt-transport-https
curl  -fsSL  https://packages.cloud.google.com/apt/doc/apt-key.gpg|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes.gpg
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt -y install vim git curl wget kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

## Token
kubeadm join 10.224.0.6:6443 --token iohdw0.0hxwc7jpucjxpjk5 \
--discovery-token-ca-cert-hash sha256:a3ac80a4989890d4deba534979fd2529fc4bb27ade514412f9c11bfc7bd0d237

helm upgrade --install kubedex kubedex -n dex