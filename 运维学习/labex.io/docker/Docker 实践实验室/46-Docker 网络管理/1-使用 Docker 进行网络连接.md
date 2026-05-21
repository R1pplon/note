# 使用 Docker 进行网络连接

在这一步中，阿周那将开始一项任务，创建一个桥接网络（bridge network）并连接神圣领域中的不同容器。

```bash
# 创建一个桥接网络
docker network create --driver=bridge divinenet

# 运行一个连接到桥接网络的容器
docker run -itd --name divine-container1 --network divinenet alpine
```
