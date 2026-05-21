# 创建自定义 RHEL 容器配置

在这一步，你将基于 Red Hat UBI 镜像创建一个自定义 Dockerfile。这个过程类似于你为自动化安装自定义 Kickstart 文件的方式，但它适用于容器环境。Dockerfile 充当创建一致 RHEL 容器部署的自动化模板。

首先，确保你在你的项目目录中：

```bash
cd ~/project
```

为我们的自动化 RHEL 容器部署创建一个新的、更具体的 Dockerfile：

```bash
cp rhel-container.dockerfile rhel9-automated.dockerfile
```

验证这两个文件都存在：

```bash
ls -l *.dockerfile
```

你应该看到这两个文件：

```plaintext
-rw-r--r--. 1 labex labex 423 Jul 22 10:30 rhel-container.dockerfile
-rw-r--r--. 1 labex labex 423 Jul 22 10:35 rhel9-automated.dockerfile
```

现在，打开新的 Dockerfile 以在自定义之前检查其结构：

```bash
nano rhel9-automated.dockerfile
```

在文件中，你将看到相当于 Kickstart 指令的容器指令：

- **FROM 指令**：指定基础 RHEL 镜像（相当于安装介质）
- **ENV 指令**：设置环境变量（相当于系统配置）
- **RUN 指令**：在镜像构建期间执行命令（相当于软件包安装）
- **EXPOSE 和 CMD**：配置服务和启动（相当于服务配置）

现在，只需按 `Ctrl+X` 退出编辑器，继续进行自定义步骤。

理解这种结构可以让你为下一步做好准备，在下一步中，你将自定义容器配置以满足特定的部署需求，就像你为自动化 VM 安装自定义 Kickstart 文件一样。
