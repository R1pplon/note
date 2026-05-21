# 创建一个 Docker 容器

在这一步中，你将创建一个 Docker 容器来代表农民的运输容器。

```bash
# Create a new Docker container
docker run --name transport-container -d alpine tail -f /dev/null
```
