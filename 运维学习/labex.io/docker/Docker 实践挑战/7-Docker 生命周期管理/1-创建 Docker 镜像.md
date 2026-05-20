# 创建 Docker 镜像

在这一步骤中，我们将使用 Dockerfile 构建一个 Docker 镜像。

## 目标

你的目标是使用 `Dockerfile` 构建一个基于 `nginx:latest` 且名为 `my-web-server` 的镜像，该镜像将包含一个内容为 `Hello Docker!` 的 html 文件。

## 结果示例

以下是你在本步骤结束时应能完成的效果示例：

1. 在 `/home/labex/project` 路径下创建一个名为 `hello-docker` 的新目录，并进入该目录。

```
labex:hello-docker/ $ pwd
/home/labex/project/hello-docker
```

2. 创建一个名为 `Dockerfile` 的文件，内容如下：

```dockerfile
FROM nginx:latest
COPY index.html /usr/share/nginx/html/
```

3. 创建一个 `index.html` 文件，内容如下：

```html
<!doctype html>
<html>
  <head>
    <title>Hello Docker!</title>
  </head>
  <body>
    <h1>Hello Docker!</h1>
  </body>
</html>
```

4. 构建名为 `my-web-server` 的 Docker 镜像。

```
labex:hello-docker/ $ docker images | grep my
my-web-server                 latest    4234a7baf5ad   56 seconds ago   187MB
```

## 要求

- 你的机器上必须已安装 Docker。
