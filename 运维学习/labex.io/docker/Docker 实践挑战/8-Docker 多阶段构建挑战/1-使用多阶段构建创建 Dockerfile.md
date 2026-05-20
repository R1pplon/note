# 使用多阶段构建创建 Dockerfile

在本节中，你将创建一个使用多阶段构建技术的 `Dockerfile`。

## 任务

- 创建一个新的 `Dockerfile`，要求包含两个阶段。第一阶段用于构建应用程序，包括安装必要的依赖项并运行构建脚本。第二阶段用于生成最终镜像，通过从第一阶段复制编译后的代码，仅安装生产环境依赖，并暴露必要的端口。

## 示例

以下是你需要执行的步骤示例：

1. 创建一个名为 `myapp` 的新目录并进入该目录。

    ```bash
    mkdir myapp
    cd myapp
    ```

2. 从 `https://github.com/labex-labs/nodejs-example.git` 下载 NodeJS 源代码。

    ```bash
    git clone https://github.com/labex-labs/nodejs-example.git
    ```

    将下载的代码移动到 `myapp` 目录根目录下。

    ```bash
    mv nodejs-example/* .
    rm -rf nodejs-example
    ```

3. 在 `myapp` 目录中创建一个名为 `Dockerfile` 的新文件，内容如下：

    ```Dockerfile
    # Stage 1: Build the application
    FROM node:14-alpine AS base
    WORKDIR /app
    COPY . ./
    RUN npm install

    # Stage 2: Create the final image
    FROM node:14-alpine
    WORKDIR /app
    COPY --from=base /app/ .
    EXPOSE 3000
    CMD [ "npm", "start" ]
    ```

## 要求

要完成此挑战，你需要：

- 在 `/home/labex/project` 目录下创建一个名为 `myapp` 的目录。
- 将 NodeJS 源代码克隆到 `myapp` 目录中。
- 在 `myapp` 目录中按照指定内容创建 `Dockerfile`。
