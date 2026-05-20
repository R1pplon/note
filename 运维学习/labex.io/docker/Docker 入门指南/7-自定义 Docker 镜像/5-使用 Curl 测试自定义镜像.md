# 使用 Curl 测试自定义镜像

让我们基于新镜像运行一个容器并测试 `curl` 工具。

1. 在 WebIDE 终端中，使用以下命令启动一个新容器：

```bash
docker run -d --name curl-container my-nginx-curl
```

此命令启动一个基于 `my-nginx-curl` 镜像的新容器，并将其命名为 `curl-container`。

2. 在运行中的容器内执行 bash shell：

```bash
docker exec -it curl-container bash
```

此命令在运行中的容器内部打开一个交互式的 bash shell。

理解该命令：

- `docker exec`：在运行中的容器内运行命令。
- `-it`：这些标志分配一个伪终端并保持标准输入（STDIN）打开，允许你与 shell 进行交互。
- `curl-container`：这是我们容器的名称。
- `bash`：这是我们在容器中运行的命令（打开 bash shell）。

3. 你现在应该看到一个新的提示符，表明你已进入容器内部。在容器内测试 `curl` 工具：

```bash
curl http://localhost
```

你应该会在终端中看到 `index.html` 文件的 HTML 内容。

4. 退出容器的 bash shell：

```bash
exit
```

此命令将带你回到宿主系统的 shell。
