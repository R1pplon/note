# 设置你的 Docker 环境

在这一步中，你要先确保你的 Docker 环境已正确设置，以便运行容器。这就好比你要为古董工作室准备好必要的工具和设备。

1. 在终端中执行以下命令来安装 Docker：

   ```bash
   sudo apt update
   sudo apt install docker.io
   ```

   第一条命令用于更新软件包列表，以确保你能获取到最新版本的 Docker；第二条命令则用于安装 Docker 本身。`sudo` 前缀用于授予安装所需的管理员权限。

2. Docker 安装完成后，启动 Docker 服务：

   ```bash
   sudo systemctl start docker
   ```

   此命令用于激活 Docker 服务，就像你打开工作室开始营业一样。现在，Docker 守护进程正在后台运行，随时准备处理你的容器请求。

3. 验证 Docker 是否成功运行：

   ```bash
   sudo systemctl status docker
   ```

   此命令用于检查 Docker 是否处于活动状态并正常运行。你应该会看到输出中带有绿色文本的“active (running)”，这表明你的 Docker 环境已准备就绪。如果你看到“inactive”或任何错误消息，则可能需要重新启动服务。

4. 为了确保你无需每次都使用 `sudo` 来运行 Docker 命令，将你的用户添加到 Docker 组：

   ```bash
   sudo usermod -aG docker $USER
   ```

   注意：运行此命令后，你可能需要注销并重新登录才能使更改生效。在本实验中，为确保兼容性，我们将继续在 Docker 命令前使用 `sudo`。
