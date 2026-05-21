# 将 Docker 镜像推送到仓库

## 任务

- 将一个 nginx 容器的 Docker 镜像推送到中央仓库
- 验证 nginx 容器镜像推送成功
- 使用版本号 1.0 为 nginx 容器镜像打标签

## 要求

- 使用 Docker 命令行界面（CLI）推送 nginx 镜像
- 假定 Docker 和必要的容器镜像已预先安装

## 示例

首先，你需要登录 Docker Hub。

然后，成功完成后，`docker push` 命令应该会显示镜像已推送到中央仓库。

```
The push refers to repository [docker.io/<your_dockerhub_name>/nginx]
009507b85609: Mounted from library/nginx
fbcc9bc44d3e: Mounted from library/nginx
b4ad47845036: Mounted from library/nginx
eddcd06e5ef9: Mounted from library/nginx
b61d4b2cd2da: Mounted from library/nginx
b6c2a8d6f0ac: Mounted from library/nginx
571ade696b26: Mounted from library/nginx
1.0: digest: sha256:9ceac672f55cdad7316651fcced0d6a1ba44e87454ec86236cb3bca647e419f6 size: 1778
```
