# 列出 Docker 镜像

在这一步骤中，你需要列出 Docker 镜像并执行一些与镜像管理相关的特定任务。

## 任务

- 列出当前系统中所有可用的 Docker 镜像。
- 过滤镜像，仅显示标签（tag）为 `latest` 的镜像。你可以使用 `docker images --filter <filter_type>=<filter_scope>` 这种命令格式来过滤镜像。
- 按创建日期降序排列镜像。你可以使用 `docker images --format <format> | grep <condition> | sort <option>` 这种命令格式来对镜像进行排序。

## 示例

完成任务后，Docker 镜像列表的输出应显示符合指定条件的镜像。

列出所有镜像：

```
REPOSITORY                    TAG       IMAGE ID       CREATED         SIZE
ubuntu                        latest    e34e831650c1   2 weeks ago     77.9MB
jenkins/jenkins               latest    ca7cca8fa4b0   8 months ago    466MB
hello-world                   latest    d2c94e258dcb   8 months ago    13.3kB
gcr.io/k8s-minikube/kicbase   v0.0.37   01c0ce65fff7   12 months ago   1.15GB
```

过滤镜像，仅显示标签为 `latest` 的镜像：

```
REPOSITORY       TAG       IMAGE ID       CREATED        SIZE
ubuntu           latest    e34e831650c1   2 weeks ago    77.9MB
jenkins/jenkins  latest    ca7cca8fa4b0   8 months ago   466MB
hello-world      latest    d2c94e258dcb   8 months ago   13.3kB
```

按创建日期降序排列镜像：

```
ubuntu                        latest    2024-01-12 01:08:11 +0800 CST   77.9MB
jenkins/jenkins               latest    2023-05-24 03:54:22 +0800 CST   466MB
hello-world                   latest    2023-05-03 00:49:27 +0800 CST   13.3kB
```
