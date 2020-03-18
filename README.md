# Kubernetes on CentOS 7
A finalidade deste projeto é documentar a instalação de um cluster Kubernetes single-master utilizando a ferramenta kubeadmin no CentOS 7

## Sistema Operacional
Versão S.O: CentOS Linux release 7.7.1908 (Core)

## Máquinas Utilizadas:
* kube-master-01 | 192.168.58.30
* kube-worker-01 | 192.168.58.31
* kube-worker-02 | 192.168.58.32


## Versões dos componentes
| componente | versão |
| ----------| ---------|
|docker-ce | 18.09.9 |
|kubeadm | 1.15.11 |
|kubectl | 1.15.11 | 
|kubelet| 1.15.11|


## Preaparação do Ambiente / Instalação de Pacotes
### Configuração do arquivo /etc/hosts
```
vim /etc/hosts

192.168.58.30 kube-master-01.example.com kube-master-01
192.168.58.31 kube-worker-01.example.com kube-worker-01
192.168.58.32 kube-worker-02.example.com kube-worker-02
```


### Configuração SELinux
```
setenforce 0
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
```

### Habilitar módulo br_netfilter
```
modprobe br_netfilter
```
```
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system

```
### Desabilitar a partição de swap
```
swapoff -a
```

```
vim /etc/fstab # Comentar a linha que monta a partição swap
```

### Instalar Prerequisitos Docker
```
yum install -y yum-utils device-mapper-persistent-data lvm2
```
### Adicionar repositório docker-ce
```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

### Adicionar repositório kubernetes
```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```


### Instalar os pacotes 
```
yum install -y kubelet kubeadm kubectl docker-ce
```

## Configurações
### Confirações Docker
```
mkdir /etc/docker
```

```
cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF
```

```
mkdir -p /etc/systemd/system/docker.service.d
```

Restart Docker

```
systemctl daemon-reload 
systemctl enable docker  
systemctl start docker  
```



## Inicializando o cluster
```
kubeadm init --apiserver-advertise-address=192.168.58.30  --pod-network-cidr=10.244.0.0/16
```

## Criando usuário comum para administrar o cluster
```
useradd kubeadmin
passwd kubeadmin
gpasswd -a kubeadmin docker
gpasswd -a kubeadmin wheel
```

```
su - kubeadmin
```

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

```

## Realizar o deploy do plugin de Rede (calico)
```
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```
