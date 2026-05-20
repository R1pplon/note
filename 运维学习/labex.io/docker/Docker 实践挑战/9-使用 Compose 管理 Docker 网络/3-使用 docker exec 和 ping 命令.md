# 使用 docker exec 和 ping 命令

在这一步中，你将学习如何将 Docker 网络与 Compose 结合使用。你将创建一个包含网络配置的简单 Compose 文件。

## 目标

创建一个包含网络配置的 Compose 文件，并使用 `docker-compose up` 运行容器。

## 结果示例

以下是你完成本挑战后应达到的效果示例：

1. 在 `~/project` 目录下创建一个名为 `docker-compose.yml` 的 Compose 文件，其中包含网络配置。

```bash
pwd
```

示例输出：

```plaintext
/home/labex/project
```

```bash
ls
```

示例输出：

```plaintext
docker-compose.yml
```

2. 使用以下命令下载 `docker-compose`：

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/v2.6.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose version
```

示例输出：

```plaintext
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                              Dload  Upload   Total   Spent    Left  Speed
0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 24.5M  100 24.5M    0     0  10.4M      0  0:00:02  0:00:02 --:--:-- 19.2M
Docker Compose version v2.6.1
```

3. 使用 `docker-compose up` 运行容器。

4. 通过从一个容器 ping 另一个容器，验证它们是否可以相互通信。

示例输出：

```plaintext
[+] Running 3/3
⠿ Network project_my-network  Created                                     0.1s
⠿ Container project-nginx-1   Created                                     0.1s
⠿ Container project-alpine-1  Created                                     0.1s
Attaching to project-alpine-1, project-nginx-1
project-alpine-1  | PING nginx (172.21.0.3): 56 data bytes
project-nginx-1   | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
project-nginx-1   | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
```
