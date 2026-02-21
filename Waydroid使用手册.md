## 准备工作

Waydroid对系统环境有两个要求，安装前需要提前检查一下：
1. **内核模块支持**：系统需加载 **binder_linux** 和 **ashmem_linux** 内核模块（Ubuntu 22.04+版本默认包含这两个模块）。
2. **Wayland桌面环境**：**Waydroid仅支持Wayland**，不兼容X11（Ubuntu登录时可切换GNOME on Wayland）。

检查方法：

```sh
lsmod | grep binder

echo  $XDG_SESSION_TYPE
```

## 安装Waydroid

```sh
# 安装依赖
sudo apt update
sudo apt install curl ca-certificates -y

# 添加官方源
curl -s https://repo.waydro.id | sudo bash

# 安装waydroid
sudo apt install waydroid -y
```

验证安装

```sh
# 检查Waydroid版本
waydroid --version

# 检查服务状态
systemctl status waydroid-container
```

