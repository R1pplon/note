# Dockerfile 指令速查笔记（实操向）
> 基于 https://www.runoob.com/docker/docker-dockerfile.html 整理，精简为可直接上手使用的命令参考。
---
## 一、基础指令（构建阶段）
| 指令             | 格式                                                           | 说明                 | 实操要点                                                                                                                 |
| -------------- | ------------------------------------------------------------ | ------------------ | -------------------------------------------------------------------------------------------------------------------- |
| **FROM**       | `FROM <image>[:<tag>]`                                       | 指定基础镜像，必须为第一条非注释指令 | 后续所有指令都基于此镜像；可多次使用多阶段构建                                                                                              |
| **MAINTAINER** | `MAINTAINER <name>`                                          | 镜像维护者信息            | **已弃用**，推荐用 `LABEL`                                                                                                  |
| **LABEL**      | `LABEL <key>=<value> ...`                                    | 添加镜像元数据（键值对）       | 例：`LABEL maintainer="you@example.com"`                                                                               |
| **RUN**        | `RUN <command>`（shell）<br>`RUN ["exec","arg1","arg2"]`（exec） | 在构建时执行命令，产生新镜像层    | ❗**每条 RUN 产生一层**，用 `&&` 合并多条命令减少层数：<br>`RUN apt-get update && apt-get install -y vim && rm -rf /var/lib/apt/lists/*` |
| **ARG**        | `ARG <name>[=<default>]`                                     | 定义构建时变量，仅构建阶段有效    | 构建：`docker build --build-arg <name>=<value> .`<br>镜像内不存在此变量                                                          |
| **ENV**        | `ENV <key> <value>`<br>`ENV <key1>=<value1> ...`             | 设置环境变量，构建+运行时均有效   | 后续指令可用 `$key` 引用；容器运行时也可见                                                                                            |
| **WORKDIR**    | `WORKDIR <path>`                                             | 设置工作目录（不存在会自动创建）   | 影响 RUN/CMD/ENTRYPOINT/COPY/ADD 的当前目录                                                                                 |
| **USER**       | `USER <user>[:<group>]`                                      | 指定后续命令执行的用户/组      | 用户必须已存在；用于降权运行                                                                                                       |
| **SHELL** | `SHELL ["exec","param"]` | 覆盖默认 shell（Linux 默认 `/bin/sh -c`） | 影响后续 RUN/CMD/ENTRYPOINT 的 shell 形式 |
---
## 二、文件操作指令
| 指令 | 格式 | 说明 | 实操要点 |
|------|------|------|----------|
| **COPY** | `COPY [--chown=<user>:<group>] <src>... <dest>`<br>`COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]` | 从上下文目录复制文件/目录到镜像 | 推荐；支持通配符：`COPY hom* /app/`<br>目标路径不存在会自动创建 |
| **ADD** | `ADD <src>... <dest>`<br>`ADD ["<src>",... "<dest>"]` | 复制文件，支持自动解压 tar（gzip/bzip2/xz）和远程 URL | ❗若不需要自动解压/下载，优先用 `COPY`；<br>ADD 会使构建缓存失效，可能拖慢构建 |
---
## 三、容器启动指令
| 指令 | 格式 | 说明 | 实操要点 |
|------|------|------|----------|
| **CMD** | `CMD <command>`（shell）<br>`CMD ["exec","arg1","arg2"]`（exec）<br>`CMD ["arg1","arg2"]`（为 ENTRYPOINT 传参） | 容器启动时默认执行命令；**可被 docker run 参数覆盖** | 多条 CMD 只有最后一条生效；<br>推荐 exec 格式；<br>若同时有 ENTRYPOINT，则 CMD 作为默认参数 |
| **ENTRYPOINT** | `ENTRYPOINT ["exec","arg1","arg2"]` | 容器启动时执行的主命令；**不会被 docker run 参数覆盖**（除非 `--entrypoint`） | 多条 ENTRYPOINT 只有最后一条生效；<br>常与 CMD 搭配：ENTRYPOINT 定参，CMD 变参 |
| **EXPOSE** | `EXPOSE <port> [<port>...]` | 声明容器运行时监听端口 | 仅声明，不会自动发布；需 `docker run -P` 随机映射或 `-p` 手动映射 |
| **VOLUME** | `VOLUME ["<path>"]`<br>`VOLUME <path>` | 创建匿名数据卷挂载点 | 防止容器重启数据丢失；<br>运行时可用 `-v` 覆盖挂载路径 |
---
## 四、健康检查与触发器
| 指令 | 格式 | 说明 | 实操要点 |
|------|------|------|----------|
| **HEALTHCHECK** | `HEALTHCHECK [选项] CMD <command>`<br>`HEALTHCHECK NONE` | 定义容器健康检查命令 | 选项：`--interval`（间隔）、`--timeout`（超时）、`--retries`（重试）<br>NONE 可屏蔽基础镜像的健康检查 |
| **ONBUILD** | `ONBUILD <其它指令>` | 延迟执行：当前镜像被别人 `FROM` 时才执行 | 常用于基础镜像，让子镜像构建时自动执行某些操作 |
| **STOPSIGNAL** | `STOPSIGNAL <signal>` | 设置停止容器时发送的系统信号 | 默认 SIGTERM；可改为 SIGKILL 等 |
---
## 五、实操要点速记
1. **镜像层数控制**：每条 RUN/COPY/ADD 都产生新层；用 `&&` 合并 RUN 命令，并清理缓存（如 `rm -rf /var/lib/apt/lists/*`）。
2. **构建上下文**：`docker build -t name:tag .` 最后的 `.` 是上下文路径，Docker 会将该目录内容发给守护进程；**不要放无用文件**，避免构建缓慢。
3. **CMD vs ENTRYPOINT**：
   - 只想一个固定命令：用 `CMD`。
   - 想让镜像像可执行程序，固定入口、可传参：用 `ENTRYPOINT` + `CMD`。
4. **ENV vs ARG**：
   - `ENV`：构建 + 运行时都在。
   - `ARG`：仅构建时在，适合传构建参数（如版本号）。
5. **COPY vs ADD**：不需要自动解压/远程下载时，优先 `COPY`，更透明、缓存友好。
6. **WORKDIR**：不要用 `RUN cd /app && ...`，改用 `WORKDIR /app`，更清晰且持久。
7. **EXPOSE**：只是文档作用，实际映射要靠 `-p` 或 `-P`。
---
## 六、典型 Dockerfile 模板
```dockerfile
# 基础镜像
FROM node:18-alpine
# 元数据
LABEL maintainer="you@example.com"
# 构建参数
ARG NODE_ENV=production
# 环境变量
ENV NODE_ENV=${NODE_ENV} \
    APP_HOME=/app
# 工作目录
WORKDIR ${APP_HOME}
# 复制依赖文件并安装
COPY package*.json ./
RUN npm ci --only=production && \
    npm cache clean --force
# 复制应用代码
COPY . .
# 声明端口
EXPOSE 3000
# 数据卷
VOLUME ["/app/data"]
# 健康检查
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
    CMD curl -f http://localhost:3000/health || exit 1
# 启动命令
CMD ["node", "server.js"]
```
---
**使用**：  
```bash
# 构建镜像
docker build -t myapp:1.0 .
# 运行容器
docker run -d -p 3000:3000 -v /host/data:/app/data myapp:1.0
```
此笔记即可作为日常编写 Dockerfile 的速查手册，按指令分类、要点明确，适合实操时快速查阅。
