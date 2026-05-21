# 构建基于 Alpine 的 Docker 镜像

## 任务

- 更新 Dockerfile，改用 Alpine 作为基础镜像。
- 使用更新后的 Dockerfile 重新构建 Docker 镜像。

## 要求

- 在 Dockerfile 中使用 Alpine 作为基础镜像。
- 更新 Dockerfile 后重新构建镜像。

## 示例

更新 Dockerfile 并重新构建后，生成的 Docker 镜像应当是基于 Alpine 的。

```
Sending build context to Docker daemon  2.048kB
Step 1/1 : FROM alpine
latest: Pulling from library/alpine
661ff4d9561e: Pull complete
Digest: sha256:51b67269f354137895d43f3b3d810bfacd3945438e94dc5ac55fdac340352f48
Status: Downloaded newer image for alpine:latest
 ---> f8c20f8bbcb6
Successfully built f8c20f8bbcb6
Successfully tagged my-nginx-alpine:latest
```
