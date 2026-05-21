# 删除特定的 Docker 镜像

## 任务

- 列出系统中存在的所有 Docker 镜像。
- 删除标签为 `nginx:latest` 的特定 Docker 镜像。

## 示例

确认 nginx 镜像已被删除。

```
labex:project/ $ docker images | grep nginx  || echo "nginx image not exists"
nginx image not exists
```
