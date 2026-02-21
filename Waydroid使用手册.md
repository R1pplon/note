## 准备工作

Waydroid对系统环境有两个要求，安装前需要提前检查一下：
1. **内核模块支持**：系统需加载 **binder_linux** 和 **ashmem_linux** 内核模块（Ubuntu 22.04+版本默认包含这两个模块）。
2. **Wayland桌面环境**：**Waydroid仅支持Wayland**，不兼容X11（Ubuntu登录时可切换GNOME on Wayland）。

