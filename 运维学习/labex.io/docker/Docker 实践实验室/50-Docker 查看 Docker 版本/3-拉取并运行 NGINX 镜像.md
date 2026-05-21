# 拉取并运行 NGINX 镜像

继续你的旅程，拉取 NGINX Docker 镜像并使用以下命令基于它运行一个容器：

```bash
docker pull nginx
docker run -d -p 8080:80 --name mynginx nginx
```
