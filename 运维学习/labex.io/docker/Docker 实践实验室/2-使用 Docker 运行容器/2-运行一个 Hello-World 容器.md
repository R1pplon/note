# 运行一个 Hello-World 容器

既然你的环境已经设置好，你将运行你的第一个 Docker 容器 —— 一个简单的“hello-world”示例。这就好比你展示第一件文物，以确保新的容器技术能按预期工作。

1. 从 Docker Hub 拉取“hello-world”镜像：

   ```bash
   sudo docker pull hello-world
   ```

   此命令从 Docker Hub（一个 Docker 镜像的公共仓库）下载“hello-world”镜像。这就像是你从远方获得了第一件文物。Docker Hub 就像一个市场，你可以在那里找到各种用途的预打包容器。

2. 运行“hello-world”容器：

   ```bash
   sudo docker run hello-world
   ```

   此命令从“hello-world”镜像创建并启动一个容器。该容器运行一个简单的程序，输出一条消息后就退出。在我们的维多利亚时代场景中，这相当于短暂展示你的第一件文物，以验证新的容器系统是否正常工作。

3. 检查输出，确保容器成功运行。你应该会看到一条以“Hello from Docker!”开头的消息，后面跟着一些解释性文本。这确认了：
   - Docker 已正确安装
   - 你的系统可以成功创建容器
   - 你可以从 Docker Hub 下载镜像

4. 查看所有容器的列表（包括已退出的容器）：

   ```bash
   sudo docker ps -a
   ```

   此命令会显示所有容器，包括现在已经退出的“hello-world”容器。你会看到容器 ID、它是从哪个镜像创建的、创建时间以及当前状态等详细信息。
