## 介绍
在本实验中，你将学习如何使用数据卷（Volumes）来管理 Docker 容器中的数据。Docker 数据卷是持久化由 Docker 容器生成和使用的数据的首选机制。本实验将引导你了解 Docker 数据卷的各个方面，包括创建、管理、数据共享、备份和恢复。通过本实验，你将对如何在 Docker 环境中有效地管理数据有一个扎实的理解。
## 理解 Docker 存储选项
在深入研究 Docker 数据卷之前，了解 Docker 中可用的不同存储选项非常重要。Docker 提供了三种主要的存储数据选项：

1. 数据卷（Volumes）：Docker 中持久化数据的首选机制。
2. 绑定挂载（Bind mounts）：将宿主机的特定路径连接到容器。
3. 临时文件系统挂载（tmpfs mounts）：将数据临时存储在宿主机的内存中。

在本实验中，我们将主要关注数据卷，因为它们是 Docker 中管理数据最灵活且最受推荐的选项。

让我们先列出系统上当前的数据卷：

```bash
docker volume ls
```

你会看到类似这样的输出：

```
DRIVER    VOLUME NAME
local     jenkins-data
```

此命令会列出系统上所有的 Docker 数据卷。输出显示了数据卷驱动程序（通常是 "local"）和数据卷名称。你可能会看到一些现有的数据卷，或者如果尚未创建任何数据卷，列表可能是空的。

如果你看到不同的卷名称或根本没有卷，请不要担心。这是正常现象，取决于你之前在系统上对 Docker 进行的操作。
## 创建和管理具名卷
现在，让我们创建一个新的具名卷（Named Volume）。具名卷是你显式创建并赋予特定名称的卷。这使得以后引用和管理它变得更加容易。

运行此命令创建一个新卷：

```bash
docker volume create my_data
```

此命令创建了一个名为 `my_data` 的新卷。Docker 将处理该卷在宿主系统上存储位置和方式的所有细节。

让我们验证卷是否已创建：

```bash
docker volume ls
```

你现在应该在卷列表中看到 `my_data`，以及之前存在的任何卷。

要获取有关该卷的更详细信息，我们可以使用检查（inspect）命令：

```bash
docker volume inspect my_data
```

这将输出类似以下内容：

```json
[
  {
    "CreatedAt": "2024-08-22T14:31:09+08:00",
    "Driver": "local",
    "Labels": {},
    "Mountpoint": "/var/lib/docker/volumes/my_data/_data",
    "Name": "my_data",
    "Options": {},
    "Scope": "local"
  }
]
```

这个输出告诉了我们关于该卷的几件事：

- 创建时间
- 正在使用的驱动程序（在本例中为 local）
- 挂载点（数据在宿主系统上实际存储的位置）
- 我们给它起的名称

如果你现在不理解所有这些细节，请不要担心。对我们来说最重要的部分是名称（Name）和挂载点（Mountpoint）。
## 在容器中使用数据卷
现在我们有了一个数据卷，让我们在容器中使用它。我们将启动一个新容器并将我们的卷挂载到其中。

运行此命令：

```bash
docker run -d --name my_container -v my_data:/app/data ubuntu:latest sleep infinity
```

让我们分解一下这个命令：

- `docker run`：告诉 Docker 运行一个新容器
- `-d`：以分离模式（在后台）运行容器
- `--name my_container`：给我们的容器起一个名字，方便以后引用
- `-v my_data:/app/data`：将我们的 `my_data` 卷挂载到容器内的 `/app/data` 目录
- `ubuntu:latest`：我们为容器使用的镜像
- `sleep infinity`：容器将运行的命令。它只是让容器无限期地保持运行状态

现在我们的容器正在运行，并且挂载了数据卷。让我们在卷中创建一些数据：

```bash
docker exec my_container sh -c "echo 'Hello from Docker volume' > /app/data/test.txt"
```

这个命令做了几件事：

- `docker exec`：允许我们在运行中的容器中执行命令
- `my_container`：我们容器的名称
- `sh -c "..."`：在容器内运行 shell 命令
- 实际的命令在我们的卷中创建了一个名为 `test.txt` 的文件，内容为 "Hello from Docker volume"

为了验证数据是否已写入，我们可以读取该文件：

```bash
docker exec my_container cat /app/data/test.txt
```

你应该会看到控制台打印出 "Hello from Docker volume" 消息。
## 在容器间共享数据
Docker 数据卷的一大优势是能够在容器之间共享数据。让我们创建另一个使用相同卷的容器：

```bash
docker run -d --name another_container -v my_data:/app/shared_data ubuntu:latest sleep infinity
```

这个命令与我们之前使用的非常相似，但我们给容器起了一个不同的名字，并将卷挂载到容器内的不同路径。

现在，让我们验证这个新容器是否可以访问我们之前创建的数据：

```bash
docker exec another_container cat /app/shared_data/test.txt
```

你应该会看到我们之前写入的相同的 "Hello from Docker volume" 消息。这表明两个容器都在访问相同的数据。

让我们从这个新容器中添加更多数据：

```bash
docker exec another_container sh -c "echo 'Data from another container' >> /app/shared_data/test.txt"
```

此命令将新行追加到我们的 `test.txt` 文件中。

现在，如果我们从任一容器检查文件的内容，我们应该能看到这两行：

```bash
docker exec my_container cat /app/data/test.txt
```

你应该在输出中看到 "Hello from Docker volume" 和 "Data from another container"。

这演示了如何使用 Docker 数据卷在容器之间共享数据，这对于许多应用程序来说非常有用。
## 数据卷备份与恢复
备份和恢复 Docker 数据卷对于数据保存至关重要。让我们走一遍这个流程：

首先，我们需要停止并删除正在使用该卷的容器。这是因为我们不能在卷正被使用时将其删除：

```bash
docker stop my_container another_container
docker rm my_container another_container
```

现在，让我们为数据卷创建一个备份：

```bash
docker run --rm -v my_data:/source:ro -v $(pwd):/backup ubuntu tar cvf /backup/my_data_backup.tar -C /source .
```

这个命令看起来可能很复杂，让我们分解一下：

- `docker run --rm`：运行一个临时容器，并在完成后将其删除
- `-v my_data:/source:ro`：将我们的卷以只读方式挂载到容器中
- `-v $(pwd):/backup`：将当前目录挂载为容器中的 /backup
- `ubuntu`：使用 Ubuntu 镜像
- `tar cvf /backup/my_data_backup.tar -C /source .`：创建卷数据的 tar 归档文件

现在，让我们删除原始卷：

```bash
docker volume rm my_data
```

要恢复数据，我们将创建一个新卷并将备份解压到其中：

```bash
docker volume create my_restored_data
docker run --rm -v my_restored_data:/dest -v $(pwd):/backup ubuntu bash -c "tar xvf /backup/my_data_backup.tar -C /dest"
```

这会创建一个新卷并将我们的备份提取到其中。

让我们验证数据是否已恢复：

```bash
docker run --rm -v my_restored_data:/app/data ubuntu cat /app/data/test.txt
```

你应该能看到我们之前创建的文件内容。
## 总结
在本实验中，你学习了如何使用 Docker 数据卷。你创建并管理了具名卷，在容器中使用了卷，在容器间共享了数据，并执行了备份和恢复操作。这些技能对于在 Docker 环境中进行有效的数据管理至关重要。

关键要点：

- Docker 数据卷提供了一种灵活且高效的方式来管理容器中的持久化数据。
- 可以使用 Docker CLI 命令轻松创建、检查和删除卷。
- 卷中的数据可以在多个容器之间共享。
- 备份和恢复卷对于数据保存至关重要，可以使用标准的 Linux 命令来实现。

当你继续使用 Docker 时，请记住将数据持久化和管理视为容器化应用程序的一个关键方面。务必规划好数据备份和恢复，特别是在生产环境中。