# 检查 Jenkins 端口和数据

在此步骤中，你将检查容器使用的网络端口和 Jenkins 主目录。

Jenkins 使用端口 `8080` 作为 Web 界面。检查已发布的端口：

```bash
docker port jenkins
```

你应该会看到端口 `8080` 已发布在宿主机上，类似于：

```plaintext
8080/tcp -> 0.0.0.0:8080
8080/tcp -> :::8080
```

现在检查容器内的 Jenkins 主目录：

```bash
docker exec jenkins ls /var/jenkins_home
```

你应该能看到 Jenkins 的数据，例如任务（jobs）、插件（plugins）、日志或配置文件：

```plaintext
config.xml
jobs
plugins
...
```

Jenkins 将其配置和任务数据保存在 `/var/jenkins_home` 中，因此该目录是进行备份或故障排查时的关键位置。
