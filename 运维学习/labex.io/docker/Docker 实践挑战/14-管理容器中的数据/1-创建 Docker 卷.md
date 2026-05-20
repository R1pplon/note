# 创建 Docker 卷

在这一步骤中，你将创建一个 Docker 卷。Docker 卷是一个用于存储持久化数据的托管对象，它独立于容器存在。

## 任务

1. 使用 `docker volume create` 命令创建一个名为 `myvolume` 的新 Docker 卷。
2. 列出所有 Docker 卷以验证 `myvolume` 是否创建成功。

## 要求

- 在 `/home/labex/project` 目录下执行所有操作。
- 创建卷时使用默认的卷驱动程序（volume driver）。

## 示例

完成此步骤后，运行 `docker volume ls` 应该显示类似以下内容的输出：

```
DRIVER    VOLUME NAME
local     myvolume
```
