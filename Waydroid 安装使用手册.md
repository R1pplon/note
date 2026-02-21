## 准备工作

Waydroid对系统环境有两个要求，安装前需要提前检查一下：

1. **内核模块支持**
    系统需加载 **binder_linux** 和 **ashmem_linux** 内核模块
    Ubuntu 22.04+版本默认包含这两个模块
2. **Wayland桌面环境**
    Waydroid仅支持**Wayland**，不兼容X11
    Ubuntu登录时可切换GNOME on Wayland

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

## 初始化Waydroid

初始化Waydroid可以选择**自动下载官方镜像**和**手动安装本地镜像**，

### 自动初始化

由于自动初始化下载太慢，不推荐用这种方法

在终端里直接输入：`sudo waydroid init` 或直接在所有软件里，找到并打开waydroid。

  

### 手动初始化

下载符合自己电脑情况的两个文件

[WayDroid - Browse /images at SourceForge.net](https://sourceforge.net/projects/waydroid/files/images/)

下载完后解压得到这两个文件： 
1. **system.img**
2. **vendor.img**

然后进行以下几步

```sh
# 停止Waydroid服务
sudo systemctl stop waydroid-container

# 创建目录
sudo mkdir -p /usr/share/waydroid-extra/images/

# 复制本地镜像
# /path/to/your/xxx改为正确文件路径
sudo cp /path/to/your/system.img /usr/share/waydroid-extra/images/
sudo cp /path/to/your/vendor.img /usr/share/waydroid-extra/images/

# 强制初始化
sudo waydroid init -f
```

## 启动Waydroid

```sh
# 启动容器服务
sudo systemctl start waydroid-container

# 打开Android界面
waydroid

# 或者使用全屏模式
waydroid show-full-ui

# 或者自定义屏幕分辨率
waydroid show-full-ui --display 1920x1080
```

  
## 运行安卓软件

