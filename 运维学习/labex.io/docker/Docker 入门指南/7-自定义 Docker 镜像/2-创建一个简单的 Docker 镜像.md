# 创建一个简单的 Docker 镜像

现在，让我们创建一个运行 Nginx Web 服务器的简单 Docker 镜像。

1. 在 WebIDE 中，导航到文件资源管理器（通常是左侧边栏的第一个图标）。
2. 在文件资源管理器面板中右键点击并选择「New File」。将其命名为 `Dockerfile`（首字母 `D` 大写，且没有文件扩展名）。
3. 点击 `Dockerfile` 将其打开。添加以下内容：

```Dockerfile
FROM nginx
COPY index.html /usr/share/nginx/html/
```

这个 Dockerfile 定义了一个基于官方 Nginx 镜像的新镜像，并将一个名为 `index.html` 的文件复制到 Nginx 的默认文档根目录。

理解 Dockerfile：

- `FROM nginx`：这一行指定了我们要构建的基础镜像。在本例中，我们使用的是官方的 Nginx 镜像。
- `COPY index.html /usr/share/nginx/html/`：这一行将我们的 `index.html` 文件复制到容器内的 Web 根目录中。

4. 在同一目录下创建一个名为 `index.html` 的新文件。你可以再次在文件资源管理器面板中右键点击并选择「New File」。
5. 打开 `index.html` 并添加以下内容：

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Hello Docker!</title>
  </head>
  <body>
    <h1>Hello Docker!</h1>
    <p>This is a custom Docker image.</p>
  </body>
</html>
```

这是一个简单的 HTML 页面，将由我们的 Nginx 服务器提供服务。

6. 在 WebIDE 中打开终端（如果关闭了之前的终端，请点击 Terminal -> New Terminal），并使用以下命令构建 Docker 镜像：

```bash
docker build -t my-nginx .
```

此命令会构建一个标签为 `my-nginx` 的新 Docker 镜像。

理解该命令：

- `docker build`：这是构建 Docker 镜像的命令。
- `-t my-nginx`：`-t` 标志为我们的镜像打上 `my-nginx` 的标签名。
- `.`：这指定了构建上下文（包含 Dockerfile 的目录）为当前目录。

7. 构建完成后，验证镜像是否创建成功：

```bash
docker images
```

你应该能在输出列表中看到 `my-nginx` 镜像。
