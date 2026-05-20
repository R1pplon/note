# 构建 Jenkins 的 Docker 镜像

安装 Docker Compose 后，你可以开始构建 Jenkins 的 Docker 镜像。

## 目标

构建 Jenkins 的 Docker 镜像。

## 结果示例

1. 在 `~/project` 目录下创建一个名为 "Dockerfile" 的文件，内容如下：

- 基于 `jenkins/jenkins:lts` 基础镜像
- 安装所需的插件
- 暴露 `8080` 和 `50000` 端口
- 设置 Jenkins 环境变量

2. 构建一个名为 `jenkins-docker` 的 Jenkins Docker 镜像。此步骤需要几分钟时间，请耐心等待。

```
labex:project/ $ docker images | grep jenkins-docker
jenkins-docker                latest    07a9a500b428   40 seconds ago   541MB
```
