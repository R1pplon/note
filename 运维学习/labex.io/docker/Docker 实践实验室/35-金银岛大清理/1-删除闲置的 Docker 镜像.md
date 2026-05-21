# 删除闲置的 Docker 镜像

## 任务

- 识别并列出系统中所有虚悬（dangling）的 Docker 镜像。
- 删除所有虚悬镜像以释放磁盘空间。

## 示例

你需要使用相关命令来列出虚悬镜像，然后将其删除。

```
REPOSITORY                    TAG       IMAGE ID       CREATED         SIZE
nginx                         latest    a8758716bb6a   3 months ago    187MB
jenkins/jenkins               latest    ca7cca8fa4b0   8 months ago    466MB
...
```

环境初始化脚本已经创建了一些虚悬镜像，它们应该会出现在列表中。如果没有显示任何虚悬镜像，请尝试重新运行初始化命令。
