# 检查 Jenkins 容器

## 当前状况

虚拟机启动时会运行一个名为 `jenkins` 的 Docker 容器。你现在接管了主机，需要确认正在运行的容器是否符合预期的 Jenkins 服务。

## 范围

- 主机命令在终端中运行。
- 容器名称为 `jenkins`。
- Jenkins 服务应监听主机端口 `8080`。

## 你的目标

在 `/home/labex/project/jenkins-docker-report.txt` 创建一个主机端检查文件，记录 Jenkins 容器的名称、镜像、状态、端口映射以及挂载的 Jenkins 主目录。

## 验收标准

- 报告文件存在于 `/home/labex/project` 目录下。
- 报告中指明了 `jenkins` 容器及其镜像。
- 报告中包含了 `8080` 端口映射信息。
- 报告中包含了 Jenkins 主目录的挂载路径。

## 提示

<details>
<summary>需要一个起点吗？</summary>

使用 `docker ps`、`docker inspect` 和 shell 重定向将证据收集到一个文本文件中。
</details>
