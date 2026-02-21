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

```sh
sudo waydroid init
```

或直接在所有软件里，找到并打开waydroid。

### 手动初始化

下载符合自己电脑情况的两个文件

[WayDroid - Browse /images at SourceForge.net](https://sourceforge.net/projects/waydroid/files/images/)

下载完后解压得到这两个文件： 
1. **system.img**
2. **vendor.img**

将文件放到 `/usr/share/waydroid-extra/images/` 目录下

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

## 关闭waydroid

```sh
waydroid session stop
sudo waydroid container stop
```

## 安卓软件

```sh
# 安装
waydroid app install xxx.apk 
```

## 扩展

```sh
# 设置多窗口模式
waydroid prop set persist.waydroid.multi_windows true

# 为应用添加触屏模拟
waydroid prop set persist.waydroid.fake_touch com.hypergryph.arknights

# 避免在窗口上出现多个鼠标指针
waydroid prop set persist.waydroid.cursor_on_subsurface true

# 设置共享文件夹为/Download文件夹（可自行修改文件夹路径）
sudo mount --bind ~/下载 ~/.local/share/waydroid/data/media/0/Download
```

```sh
sudo apt install lzip sqlite3

# 克隆waydroid_script仓库
git clone https://github.com/casualsnek/waydroid_script
# 切换至waydroid_script目录
cd waydroid_script

# 创建虚拟环境
python3 -m venv venv

# 安装脚本需要的依赖
venv/bin/pip install -r requirements.txt

# 执行waydroid_scrip脚本
sudo venv/bin/python3 main.py
```

选择安卓版本和应用
在安卓内查看系统信息
应用推荐选择
- `magisk` 面具 获取root权限
- `libhoudini` Arm转译

![](assets/Waydroid%20安装使用手册/file-20260221211820075.png)

## 代理抓包

### 导出证书

从 BurpSuite 导出证书 `cacert.der`

```sh
# 转换为 PEM（Android 需要 PEM 格式）
openssl x509 -inform DER -in cacert.der -out cacert.pem

# ## 安装自签名的CA证书
sudo venv/bin/python3 main.py install mitm --ca-cert ./cacert.pem
```

### burp suite 设置透明代理

burp suite 的 代理设置，修改监听器
**勾选 Support invisible proxying** 
![](assets/Waydroid%20安装使用手册/file-20260222010415555.png)

### 配置 iptables 透明代理规则

将来自 `waydroid0` 网卡的所有 TCP 流量重定向到 Burp 的 8080 端口

```sh
sudo iptables -t nat -A PREROUTING -i waydroid0 -p tcp -j REDIRECT --to-ports 8080

# **验证规则是否添加成功**
sudo iptables -t nat -L -n -v
Chain PREROUTING (policy ACCEPT 2254 packets, 479K bytes)
 pkts bytes target     prot opt in     out     source               destination         
  236 14176 REDIRECT   tcp  --  waydroid0 *       0.0.0.0/0            0.0.0.0/0            redir ports 8080
```