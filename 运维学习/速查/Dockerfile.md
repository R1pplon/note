根据你提供的教程，这里为你整理了一份精简、面向实操的Dockerfile指令速查笔记。

### 📘 Dockerfile 核心指令速查

| 指令 | 核心作用与关键点 |
| :--- | :--- |
| **FROM** | **指定基础镜像**。必须为第一条指令。例：`FROM nginx:1.21` |
| **LABEL** | **添加镜像元数据**，如作者、版本。例：`LABEL author="your_name"` |
| **ENV** | **设置持久环境变量**，构建和运行容器时均存在。例：`ENV NODE_VERSION 16` |
| **ARG** | **定义构建变量**，仅构建时有效，可用于传递参数。例：`ARG build_type=prod` |
| **WORKDIR** | **指定工作目录**，若不存在则自动创建。后续指令在容器内的此目录执行。例：`WORKDIR /app` |
| **RUN** | **在镜像构建时执行命令**。注意用 `&&` 连接命令来减少镜像层数。例：`RUN apt-get update && apt-get install -y curl` |
| **COPY** | **从构建上下文复制文件/目录到镜像**。推荐使用，功能纯粹。例：`COPY ./src /app/src` |
| **ADD** | **功能增强的复制**。源路径可为URL，且会自动解压tar文件，需谨慎使用。例：`ADD archive.tar.gz /tmp` |
| **CMD** | **指定容器启动时的默认命令**，`docker run` 后加命令可覆盖。若多条，仅最后一条生效。 |
| **ENTRYPOINT** | **定义容器主命令，不会被普通启动命令覆盖**。可用 `--entrypoint` 覆盖。常与 `CMD` 配合传递默认参数。 |
| **EXPOSE** | **声明容器监听的端口**，主要起文档作用。使用 `docker run -P` 时会随机映射这些端口。例：`EXPOSE 80 443` |
| **VOLUME** | **创建匿名数据卷**，避免重要数据丢失，防止容器体积膨胀。例：`VOLUME /data` |
| **USER** | **切换后续指令和容器运行时的用户**。需用户已存在。例：`USER appuser` |
| **HEALTHCHECK** | **定义容器健康检查命令**，用于监控服务状态。例：`HEALTHCHECK CMD curl -f http://localhost/ || exit 1` |
| **ONBUILD** | **定义触发器**，当本镜像作为另一个Dockerfile的基础镜像时执行。 |
| **SHELL** | **覆盖默认Shell**，影响 `RUN`、`CMD`、`ENTRYPOINT`。例：`SHELL ["/bin/bash", "-c"]` |

### 💡 关键实践要点
*   **层合并**：将 `RUN` 命令用 `&&` 连接，可以有效减少镜像层数。
*   **变量作用域**：`ENV` 定义的变量在容器运行时也可用；`ARG` 定义的变量仅在构建过程中可用。
*   **命令组合**：`ENTRYPOINT` 设定固定入口，`CMD` 提供可被 `docker run` 末尾参数覆盖的默认参数，形成灵活组合。

如果需要，你可以尝试用上面的指令写一个简单的Dockerfile，我帮你看看有没有优化空间。