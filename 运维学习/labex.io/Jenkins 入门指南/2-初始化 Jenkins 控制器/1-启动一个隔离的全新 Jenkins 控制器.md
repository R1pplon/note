# 启动一个隔离的全新 Jenkins 控制器

在此步骤中，你将启动一个新的 Jenkins 控制器，而不会更改主 Jenkins 服务。Jenkins 控制器将其配置存储在 `/var/jenkins_home` 下，因此本实验使用了一个名为 `jenkins-init-lab-home` 的独立 Docker 数据卷。

下方的 `docker run` 命令会在后台启动临时控制器。`-d` 选项表示后台运行模式，`--name` 为容器指定一个稳定的名称，`-p 18080:8080` 将 Jenkins 发布到宿主机的 `18080` 端口，`-v` 则挂载隔离的 Jenkins 主目录数据卷。

```bash
docker run -d \
  --name jenkins-init-lab \
  -p 18080:8080 \
  -v jenkins-init-lab-home:/var/jenkins_home \
  jenkins/jenkins:latest
```

Docker 会输出新的容器 ID。每次生成的 ID 都不相同，因此任何长十六进制值都是正常的。

接下来，等待 Jenkins 开始响应 HTTP 请求。下方的循环会不断向本地控制器请求 `/login` 路径，直到它返回 HTTP 状态码。`200` 响应表示登录页面已可用。

```bash
until [ "$(curl -s -o /dev/null -w '%{http_code}' http://localhost:18080/login)" = "200" ]; do
  echo "Waiting for the temporary Jenkins controller..."
  sleep 5
done
```

你应该会看到几次等待提示，随后命令提示符会返回。确认容器和端口映射：

```bash
docker ps --filter name=jenkins-init-lab --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'
```

输出应包含容器名称和 `18080` 端口：

```plaintext
NAMES              STATUS          PORTS
jenkins-init-lab   Up ...          0.0.0.0:18080->8080/tcp, ...
```
