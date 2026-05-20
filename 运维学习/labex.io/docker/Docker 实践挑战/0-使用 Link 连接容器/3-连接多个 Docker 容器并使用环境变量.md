# 连接多个 Docker 容器并使用环境变量

在上一阶段的基础上，本部分增加了两个新概念：一个应用程序容器同时连接到两个服务，并且该容器通过环境变量接收连接设置。在步骤 2 中，你仅证明了已连接的容器可以相互访问。在这一步中，你必须使用两个 `--link` 选项以及描述 MySQL 和 Apache 主机的环境变量来启动 `my_app2`。

## 任务

启动三个容器：一个运行 Apache，一个运行 MySQL，一个运行自定义应用程序。自定义应用程序应该能够同时访问 MySQL 和 Apache。

## 要求

- 你需要三个 Docker 容器：Apache、MySQL 和自定义应用程序。
- 启动 `my_app2` 时需包含两个链接，并设置以下环境变量：`DB_HOST=mysql`、`DB_USER=root`、`DB_PASSWORD=password` 和 `APACHE_HOST=apache`。
- 在测试 Apache 连接之前，在 `my_app2` 中安装 `curl`，因为 `my-app` 镜像默认不包含它。
- 机器上必须已安装 Docker。

## 示例结果

1. 按照步骤 2 中的说明启动 Apache 和 MySQL 容器。
2. 启动一个名为 `my_app2` 的自定义应用程序容器，使其能够同时访问 MySQL 和 Apache。

   ```bash
   labex:project/ $ docker ps | grep app2
   8945b42659a6 my-app "python app.py" 15 seconds ago Up 15 seconds 80/tcp my_app2
   ```

3. 进入 `my_app2`，安装 `curl`，并运行 `curl http://apache/` 以确认已连接的 Apache 容器是可达的。

   ```html
   <html>
     <body>
       <h1>It works!</h1>
     </body>
   </html>
   ```
