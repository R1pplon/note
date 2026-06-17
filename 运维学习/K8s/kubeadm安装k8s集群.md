操作系统: Rockylinux 10
Docker: 20+
K8s: 1.23.6

k8s-master: 192.168.20.80
k8s-node1: 192.168.20.81
k8s-node2: 192.168.20.82

## 初始操作

```bash
# 关闭防火墙
systemctl stop firewalld
systemctl disable firewalld

# 关闭selinux
sed -i 's/enforcing/disabled/' /etc/selinux/config  # 永久
setenforce 0  # 临时

# 关闭swap
swapoff -a  # 临时
sed -ri 's/.*swap.*/#&/' /etc/fstab    # 永久

# 关闭完swap后，一定要重启一下虚拟机！！！
# 根据规划设置主机名
hostnamectl set-hostname <hostname>

# 在master添加hosts
cat >> /etc/hosts << EOF
192.168.20.80 k8s-master
192.168.20.81 k8s-node1
192.168.20.82 k8s-node2
EOF


# 将桥接的IPv4流量传递到iptables的链
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system  # 生效


# 时间同步
yum install ntpdate -y
ntpdate time.windows.com
```

## 安装docker

skip

## 添加阿里云yum源

```bash
cat > /etc/yum.repos.d/kubernetes.repo << EOF  
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0

gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```

## 安装 kubeadm、kubelet、kubectl

```bash
yum install -y kubelet-1.23.6 kubeadm-1.23.6 kubectl-1.23.6 
systemctl enable kubelet  

# 配置关闭 Docker 的 cgroups，修改 /etc/docker/daemon.json，加入以下内容  
"exec-opts": ["native.cgroupdriver=systemd"]  

# 重启 docker  
systemctl daemon-reload  
systemctl restart docker
```