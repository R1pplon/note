# 创建并挂载 Docker 数据卷

## 任务

1. 创建一个名为 `data_volume` 的新 Docker 数据卷。
2. 使用 Alpine 镜像运行一个新容器。将 `data_volume` 数据卷挂载到容器内的 `/app` 目录。在 `/app` 目录下创建一个名为 `hello.txt` 的文件，内容为「Hello, Docker volumes.」。确保容器在后台持续运行。

## 要求

为了成功完成此挑战，请遵循以下指南：

- 在 `/home/labex/project` 目录下执行所有操作。
- 容器使用 Alpine 镜像。
- 将容器命名为 `volume_mounter`。
- `hello.txt` 的内容必须精确地为 `Hello, Docker volumes.`。
- 使用 Docker 命令来创建数据卷并运行容器。
- 将数据卷挂载到容器内的 `/app` 路径。
- 确保容器在后台运行。

## 示例

完成任务后，通过运行以下命令来验证你的工作：

1. 检查数据卷是否已创建：

```bash
docker volume ls | grep data_volume
```

这应该会列出你创建的 `data_volume`。

2. 检查容器状态：

```bash
docker ps | grep volume_mounter
```

这应该会显示你的 `volume_mounter` 容器处于运行状态。

3. 检查容器以验证数据卷挂载情况：

```bash
docker inspect volume_mounter --format '{{ range .Mounts }}{{ if eq .Destination "/app" }}{{ .Name }}{{ end }}{{ end }}'
```

这应该输出 `data_volume`，确认数据卷已正确挂载。

4. 检查创建的文件是否存在：

```bash
docker exec volume_mounter cat /app/hello.txt
```

这应该显示 `hello.txt` 文件的内容。
