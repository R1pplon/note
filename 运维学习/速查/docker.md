## 基础操作

```bash
# 列出镜像
docker images

# 删除镜像
docker rmi <镜像>

# 拉取镜像
docker pull <镜像>

# 搜索镜像
docker search <镜像>

# 保存镜像
docker save <镜像>  > <文件名.tar>

# 加载镜像
docker load < <文件名.tar>

# 镜像标记
# 不会创建新镜像，只是为现有镜像创建新的名称
docker tag nginx:latest my-nginx:v1

# 列出运行中的容器
docker ps

# 列出所有容器
dockerps -a

# 容器操作
docker stop <容器>
docker start <容器>
docker restart <容器>
```

## docker run

```bash
# 基本语法
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

### 后台运行与命名

```bash
docker run -d --name my-nginx nginx
```

- `-d`：后台运行（detached mode）
- `--name`：自定义容器名（未指定时自动生成）

## Dockerfile

```
docker build
```

## docker inspect
