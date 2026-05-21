# 创建 Docker 网络

在此步骤中，你将创建 Docker 网络，以便为街头艺人团体之间的通信提供便利。

## 任务

1. 在本地机器上初始化 Docker Swarm，以启用覆盖网络（overlay networking）功能。
2. 创建一个名为 `performers_net` 的 Docker 桥接网络（bridge network），作为主要的通信通道。
3. 为北部和南部表演团体分别创建名为 `north_group` 和 `south_group` 的可附加覆盖网络（attachable overlay networks）。

## 要求

- 你应在 `/home/labex/project` 目录下执行所有任务。
- 使用 zsh 终端环境进行操作。
- `north_group` 和 `south_group` 网络必须创建为覆盖网络，以便在下一步中让独立容器加入。

## 示例

使用以下命令查看所有网络：

```
labex:~/ $ docker network ls
NETWORK ID     NAME              DRIVER    SCOPE
198184572ff6   bridge            bridge    local
617fdeb36f45   docker_gwbridge   bridge    local
91199fc6ad2e   host              host      local
n0kf1zdlvw7l   ingress           overlay   swarm
32e2857073a9   minikube          bridge    local
1078d2c781b6   none              null      local
9b0hinxvujzv   north_group       overlay   swarm
086cd01ddb77   performers_net    bridge    local
gmlf217utnhq   south_group       overlay   swarm
```
