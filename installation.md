update the server
```
apt update
```
change the hostname of the server
```
hostnamectl set-hostname k8smaster.hashlabs.in
```
```
exec bash
```
make entries into /etc/hosts file
```
vim /etc/hosts
xx.xx.xx.xx	k8smaster.hashlabs.in	k8smaster
xx.xx.xx.xx	k8sworker1.hashlabs.in	 k8sworker1
xx.xx.xx.xx	k8sworker2.hashlabs.in	 k8sworker2
:wq!
```
swapoff
```
swapoff -a
```
make it to fstab entry
```
vim /etc/fstab
comment out the swap line
#/swap.img       none       swap       sw       0       0
:wq!
```
```
sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF
```
```
sudo modprobe overlay
```
```
sudo modprobe br_netfilter
```
```
sudo tee /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
```
```
sudo sysctl --system
```
```
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
```
```
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
```
```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```
sudo apt update
```
```
sudo apt install -y containerd.io
```
```
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
```
```
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
```
```
sudo systemctl restart containerd
```
```
sudo systemctl enable containerd
```
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```
```
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
```
```
sudo cp /etc/apt/trusted.gpg /etc/apt/trusted.gpg.d
```
```
apt update
```
```
apt install -y kubelet kubeadm kubectl
```
```
apt-mark hold kubelet kubeadm kubectl
```
```
sudo kubeadm init \
  --pod-network-cidr=10.10.0.0/16 \
  --control-plane-endpoint=k8smaster.hashlabs.in
```
```  
mkdir -p $HOME/.kube
```
```
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```
```
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

To print the token
```	
kubeadm token create --print-join-command
```
To download the calico.yaml file
```
curl https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml -O
```
To apply the calico.yaml
```
kubectl apply -f calico.yaml
```

## To install in the worker node

update the server
```
apt update
```
change the hostname of the server
```
hostnamectl set-hostname k8sworker1.hashlabs.in
```
```
exec bash
```
make entries into /etc/hosts file
```
vim /etc/hosts
xx.xx.xx.xx	k8smaster.hashlabs.in	k8smaster
xx.xx.xx.xx	k8sworker1.hashlabs.in	 k8sworker1
xx.xx.xx.xx	k8sworker2.hashlabs.in	 k8sworker2
:wq!
```
swapoff
```
swapoff -a
```
make it to fstab entry
```
vim /etc/fstab
comment out the swap line
#/swap.img       none       swap       sw       0       0
:wq!
```
```
sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF
```
```
sudo modprobe overlay
```
```
sudo modprobe br_netfilter
```
```
sudo tee /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
```
```
sudo sysctl --system
```
```
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
```
```
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
```
```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```
sudo apt update
```
```
sudo apt install -y containerd.io
```
```
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
```
```
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
```
```
sudo systemctl restart containerd
```
```
sudo systemctl enable containerd
```
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```
```
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
```
```
sudo cp /etc/apt/trusted.gpg /etc/apt/trusted.gpg.d
```
```
apt update
```
```
apt install -y kubelet kubeadm kubectl
```
```
apt-mark hold kubelet kubeadm kubectl
