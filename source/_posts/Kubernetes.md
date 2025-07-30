---
title: Kubernetes
tags:
  - Kubernetes
  - Docker
categories:
  - Kubernetes
cover: https://image.runtimes.cc/202404051530299.png
abbrlink: 11340
date: 2023-11-07 11:21:07
updated: 2023-11-07 11:21:09
---
## 主节点上操作
定义主机名字

```shell
sudo vim /etc/hosts

192.168.11.128 k8s-master
192.168.11.129 k8s-node01
192.168.11.130 k8s-node01
```

修改主机名
```shell
# 打印hostname
hostname
# 修改hostname,需要重新连接,才能看到变化
sduo hostnamectl set-hostname xxx
```

下载k8s需要的镜像

```shell
# 打印所需要的镜像
kubeadm config images list

# docker pull 上面命令的镜像
# 比如
sudo docker pull registry.k8s.io/kube-apiserver:v1.28.3
sudo docker pull registry.k8s.io/kube-controller-manager:v1.28.3
sudo docker pull registry.k8s.io/kube-scheduler:v1.28.3
sudo docker pull registry.k8s.io/kube-proxy:v1.28.3
sudo docker pull registry.k8s.io/pause:3.9
sudo docker pull registry.k8s.io/etcd:3.5.9-0
sudo docker pull registry.k8s.io/coredns/coredns:v1.10.1
```

创建master节点

```shell
sudo kubeadm init --apiserver-advertise-address=192.168.110.128 --pod-network-cidr=172.16.0.0/16

# 创建完之后会提示,并且执行
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

查看k8s状态

```shell
$ kubectl get node
NAME         STATUS     ROLES           AGE    VERSION
k8s-node01   NotReady   control-plane   4m9s   v1.28.2
```

使用网络插件

```shell
curl https://docs.projectcalico.org/manifests/calico.yaml -O

kubectl apply -f calico.yaml
```



