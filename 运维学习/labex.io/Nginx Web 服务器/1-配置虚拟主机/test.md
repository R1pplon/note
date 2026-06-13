# 简介

在 Web 托管领域，在单台服务器（计算机）上托管多个网站是非常常见的做法。你可以把服务器想象成一栋大型公寓楼。就像一栋建筑可以容纳许多不同的家庭居住在不同的公寓里一样，一台 Nginx 服务器也可以托管许多不同的网站。

在 Nginx 中，这一特性被称为 **服务器块（server blocks）**。在 Apache 和许多旧教程中，类似的概念通常被称为 **虚拟主机（virtual host）**。

其核心思想是基于名称的托管。单个服务器进程可以根据客户端在 HTTP 请求中发送的主机名，来决定使用哪种网站配置。

通过使用服务器块，你可以告诉 Nginx 根据请求的主机名做出不同的响应。这是现代 Web 托管的基础，因为一台机器通常需要服务于多个网站或应用程序。

在本实验中，你将学习如何：

1. 为新网站创建一个专用文件夹（文档根目录）。
2. 为该网站编写配置文件（“规则手册”）。
3. 告诉 Nginx 监听该特定的网站名称。
4. 重启 Nginx 以使新网站生效。

我们将在你的服务器上模拟一个域名 `site1.test`，以便你查看实际效果。
# 设置网站目录

“文档根目录（Document Root）”就是存放网站文件的文件夹。当有人请求你的网站时，Nginx 需要确切地知道去哪里查找文件。

这就是静态文件服务模型：Web 服务器将请求的 URL 映射到磁盘上的文件，并在规则匹配时直接返回这些文件。

在这一步中，我们将为 `site1.test` 创建一个文件夹，并在其中放置一个简单的 HTML 页面。

首先，创建目录。我们将把它放在 `/var/www/` 下，这是 Linux 上 Web 文件的标准存放位置。

```bash
sudo mkdir -p /var/www/site1
```

接下来，我们需要确保你有权编辑此文件夹。默认情况下，`sudo` 会使 `root` 用户成为所有者。我们希望更改所有权，以便你（当前用户）可以轻松编辑文件，而无需每次都使用 `sudo`。

```bash
sudo chown -R $USER:$USER /var/www/site1
```

现在，让我们创建主页。我们将创建一个名为 `index.html` 的文件。这是任何网站主页的标准命名。

我们将使用 `nano`，这是一个终端中对初学者友好的文本编辑器。

```bash
nano /var/www/site1/index.html
```

在编辑器中输入或粘贴以下 HTML 代码：

```html
<html>
  <head>
    <title>Welcome to Site 1</title>
  </head>
  <body>
    <h1>Success! Site 1 is working!</h1>
  </body>
</html>
```

**如何在 Nano 中保存：**

1. 按 `Ctrl + O`（然后按 `Enter` 确认文件名）。
2. 按 `Ctrl + X` 退出编辑器。

让我们通过将文件内容打印到屏幕上来仔细检查文件是否创建正确：

```bash
cat /var/www/site1/index.html
```
# 创建配置文件

Nginx 遵循“配置文件”中的指令。在 Ubuntu 上，Nginx 预先配置为自动查找 `/etc/nginx/conf.d/` 目录中所有以 `.conf` 结尾的文件。

这种基于文件的设计是 Nginx 在运维工作中广受欢迎的原因之一。每个网站或功能都可以在配置中以声明方式进行描述，作为文本进行审查，并在重新加载前进行测试。

这种设置非常方便，因为它允许我们将每个网站的配置保存在各自独立的文件中。这比为每个网站维护一个巨大且混乱的文件要整洁得多。

在这一步中，我们将为我们的新网站创建一个空的配置文件。

首先，进入配置目录：

```bash
cd /etc/nginx/conf.d/
```

现在，创建一个名为 `site1.conf` 的新空文件。以网站名称命名有助于保持条理。

```bash
sudo touch site1.conf
```

验证文件是否已创建：

```bash
ls -l site1.conf
```

目前，该文件是空的。在下一步中，我们将添加必要的规则。
# 定义服务器块

现在我们需要在文件中编写指令（服务器块）。这会告诉 Nginx：“当有人请求 `site1.test` 时，请提供 `/var/www/site1` 中的文件。”

使用 `nano` 编辑器打开该文件：

```bash
sudo nano /etc/nginx/conf.d/site1.conf
```

准确输入以下配置。请务必小心标点符号：每个指令通常以分号 `;` 结尾，块由花括号 `{ }` 包围。

```nginx
server {
    listen 80;
    server_name site1.test;

    root /var/www/site1;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

**配置解析：**

- `server { ... }`：定义一个新的服务器块（一个独立的网站）。
- `listen 80;`：告诉 Nginx 在 80 端口监听标准 Web 流量（HTTP）。
- `server_name site1.test;`：**关键步骤**。这是 Nginx 监听的域名。如果收到针对 "site1.test" 的请求，此块将负责处理。

这就是让单个 Nginx 实例托管多个网站的原因。不同的 `server_name` 值可以指向不同的文档根目录、应用程序后端或行为。

- `root /var/www/site1;`：告诉 Nginx 该网站的文件在磁盘上的位置。
- `index index.html;`：指定如果用户没有请求特定页面时要提供的默认文件。
- `location / { ... }`：此块处理文件的查找方式。`try_files` 行基本上是说：“尝试查找该文件；如果找不到，则显示 404 Not Found 错误。”

保存文件（`Ctrl+O`，`Enter`）并退出（`Ctrl+X`）。
# 测试并启用配置

我们已经创建了文件，但 Nginx 尚未加载它。在告诉 Nginx 加载它之前，我们必须确保没有拼写错误。Nginx 为此内置了一个测试工具。

运行配置测试：

```bash
sudo nginx -t
```

你应该看到输出以以下内容结尾：`syntax is ok` 和 `test is successful`。

如果你看到错误，`nginx -t` 会告诉你具体哪一行出错了。请返回并使用 `nano` 进行修复。

测试通过后，安全地重新加载 Nginx。我们使用 `reload` 而不是 `restart`，因为 `reload` 在更新设置的同时保持服务器为其他用户运行。

这是生产环境中的常见习惯。重新加载应用配置更改时的干扰较小，因为服务不需要完全关闭再重新启动。

```bash
sudo service nginx reload
```

现在，让我们验证你的“虚拟主机”是否正常工作。我们将使用 `curl` 来访问该域名。

*注意：在本实验的设置中，我们欺骗系统认为 `site1.test` 指向你的本地机器 (`127.0.0.1`)，因此此命令可以在本地运行。*

```bash
curl http://site1.test
```

你应该看到你的 HTML 成功消息：

```html
<html>
  <head>
    <title>Welcome to Site 1</title>
  </head>
  <body>
    <h1>Success! Site 1 is working!</h1>
  </body>
</html>
```

这证实了 Nginx 收到了对 "site1.test" 的请求，将其匹配到了你的新服务器块，并提供了正确的文件。
# 总结

在本实验中，你成功地在 Nginx 中配置了一个虚拟主机（服务器块）。你学习了如何：

1. **设置文档根目录**：为你的网站文件创建一个专用目录。
2. **创建服务器块**：在 `/etc/nginx/conf.d/` 中创建一个将域名映射到该目录的配置文件。
3. **验证并重新加载**：在应用更改前检查语法错误。

这是 Web 托管的一项基本技能，使你能够在一台服务器上高效地运行多个网站（如 `example.com`、`blog.example.com`、`test.com`）。
