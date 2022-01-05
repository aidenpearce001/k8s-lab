```
sudo apt-get update
sudo apt-get install -y bash-completion binutils
echo 'colorscheme ron' >> ~/.vimrc
echo 'set tabstop=2' >> ~/.vimrc
echo 'set shiftwidth=2' >> ~/.vimrc
echo 'set expandtab' >> ~/.vimrc
echo 'source <(kubectl completion bash)' >> ~/.bashrc
echo 'alias k=kubectl' >> ~/.bashrc
echo 'alias c=clear' >> ~/.bashrc
echo 'complete -F __start_kubectl k' >> ~/.bashrc
sed -i '1s/^/force_color_prompt=yes\n/' ~/.bashrc
```

```
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```

```
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

```
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

```
sudo apt-get update
sudo apt-cache madison kubeadm | head -10 # check available 
KUBE_VERSION=1.22.0
sudo apt-get install -y \
  docker.io \
  kubelet=${KUBE_VERSION}-00 \
  kubeadm=${KUBE_VERSION}-00 \
  kubectl=${KUBE_VERSION}-00
sudo apt-mark hold kubelet kubeadm kubectl
```

```
sudo bash -c 'cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "storage-driver": "overlay2"
}
EOF'

sudo mkdir -p /etc/systemd/system/docker.service.d

# Restart docker.
sudo systemctl daemon-reload
sudo systemctl restart docker

# start docker on reboot
sudo systemctl enable docker

sudo docker info | grep -i "storage"
sudo docker info | grep -i "cgroup"

# start kubelet on reboot
sudo systemctl enable kubelet
sudo systemctl start kubelet
```

## Master 
```
sudo hostnamectl set-hostname master
```

```
sudo kubeadm init --kubernetes-version=${KUBE_VERSION}
```

for rejoin

kubeadm token generate
kubeadm token create e57nz5.zks2c2m3om8b2zwg --print-join-command
kubectl get nodes

## Worker
```
kubeadm join 172.31.2.218:6443 --token yrcf6m fr36h83in7frw843 \
    --discovery-token-ca-cert-hash sha256:53d25b476b3faa90901c1eab7e275b7d2290d3688af8c6dfc510d29d647f46f
```

```
sudo kubeadm reset
```