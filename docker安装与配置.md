卸载旧版本

```sh
sudo dnf remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```

设置仓库

安装 `dnf-plugins-core` 软件包（该软件包提供了管理您的 DNF 仓库的命令）并设置仓库。

```sh
sudo dnf -y install dnf-plugins-core
sudo dnf config-manager addrepo --from-repofile https://download.docker.com/linux/fedora/docker-ce.repo

# 清华 Docker CE 软件仓库
sudo sed -i 's+https://download.docker.com+https://mirrors.tuna.tsinghua.edu.cn/docker-ce+' /etc/yum.repos.d/docker-ce.repo

# 安装
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

启动并设置用户组

```sh
# 启动并设置开机自启
sudo systemctl enable --now docker

# 添加docker用户组
sudo groupadd docker

# 将当前用户加入docker组
sudo usermod -aG docker $USER

# 激活组权限变更
newgrp docker
```

配合zim框架提供docker补全命令

```sh
mkdir -p ~/.zim/modules/docker/functions/

docker completion zsh > ~/.zim/modules/docker/functions/_docker

echo "zmodule docker --use mkdir" >> .zimrc
```