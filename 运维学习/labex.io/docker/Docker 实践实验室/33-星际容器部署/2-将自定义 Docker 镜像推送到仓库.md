# 将自定义 Docker 镜像推送到仓库

## 任务

- 使用 Dockerfile 构建一个名为 `custom-image:1.0` 的自定义 Docker 镜像
- 将自定义镜像推送到中央仓库
- 验证自定义镜像推送成功

## 要求

- 在工作目录中为自定义镜像创建一个 Dockerfile
- 此任务的工作目录是 `/home/architect/project`
- 假定 Docker 和必要的容器镜像已预先安装

## 示例

成功完成后，`docker push` 命令应该会显示自定义镜像已推送到中央仓库。

```
The push refers to repository [docker.io/<your_dockerhub_name>/custom-image]
009507b85609: Mounted from library/custom-image
fbcc9bc44d3e: Mounted from library/custom-image
...
1.0: digest:
...
```
