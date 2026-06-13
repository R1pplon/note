# 简介

想象一下你的网站上有一个 VIP 专区。你不想让任何人随意进入；你希望他们先出示身份证件。在 Web 术语中，这被称为 **身份验证（Authentication）**。

在本实验中，你将学习如何在 Nginx 上实现 **基本身份验证（Basic Authentication）**。这是保护 Web 内容最简单的方法之一：当有人访问受保护的页面时，浏览器会提示输入用户名和密码。

基本身份验证配置简单，对于内部工具、临时管理页面或低复杂度的保护场景非常有用。它虽然不是一个完整的身份认证平台，但由于在运维工作中经常遇到，理解它仍然非常重要。

同时，了解其局限性也很重要。基本身份验证以一种简单的形式发送凭据，在实际部署中通常应配合 HTTPS 使用以确保安全。

你将完成以下任务：

1. 安装创建加密密码所需的工具。
2. 创建一个包含授权用户的「访客名单」（密码文件）。
3. 配置 Nginx，在允许访问前检查此名单。
4. 测试安全性，确保未经授权的访问者被拦截。
# 安装密码工具

为了保护我们的网站，我们需要一种创建和加密密码的方法。Nginx 没有内置此工具，因此我们使用一个名为 `htpasswd` 的标准实用程序。

有趣的是，`htpasswd` 是 `apache2-utils` 软件包的一部分（最初是为 Apache Web 服务器制作的），但它与 Nginx 也能完美配合。

这提醒我们，Web 生态系统中的工具通常可以在不同的服务器之间复用。即使辅助工具来自 Apache，Nginx 依然可以强制执行身份验证策略。

首先，更新你的软件包列表：

```bash
sudo apt update
```

接下来，安装该工具包：

```bash
sudo apt install apache2-utils -y
```

通过检查版本来验证工具是否安装正确：

```bash
htpasswd -v
```

你应该能看到打印出的版本号，这确认你已经准备好创建凭据了。
# 创建用户凭据

现在你将创建「访客名单」——一个包含用户名和加密密码的文件。每当有人尝试登录时，Nginx 都会检查此文件。

我们将在 `/etc/nginx/` 目录下创建一个名为 `.htpasswd` 的隐藏文件，并添加一个名为 `user1` 的用户。

运行以下命令：

```bash
sudo htpasswd -c /etc/nginx/.htpasswd user1
```

**重要警告：**

`-c` 标志代表 **Create（创建）**。它会创建一个 *新* 文件。如果你对现有文件使用 `-c`，**它将清除所有现有用户**！请仅在添加第一个用户时使用它。

当提示输入密码时，输入 `labex`。你需要输入两次。
*注意：输入时屏幕上不会显示字符。这是正常的安全行为。*

让我们检查文件是否已创建及其内容：

```bash
cat /etc/nginx/.htpasswd
```

你应该看到类似 `user1:$apr1$xyz...` 的内容。那些乱码就是加密后的密码。Nginx 可以读取它，但人类无法轻易还原。

因此，密码文件存储的是哈希值，而不是明文密码。对于任何凭据存储来说，这都是一项基本但重要的安全属性。
# 配置 Nginx 身份验证

现在我们需要告诉「门卫」（Nginx）在允许任何人进入 `/secure` 目录之前，先检查「访客名单」（密码文件）。

打开默认配置文件：

```bash
sudo nano /etc/nginx/sites-enabled/default
```

我们需要为我们的受保护文件夹专门添加一个新的 `location` 块。在 `server { ... }` 块内向下滚动，并添加以下行：

```nginx
    location /secure {
        auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
```

**这些代码的作用是什么？**

1. `location /secure { ... }`：仅当用户访问 `http://your-site/secure` 时应用这些规则。
2. `auth_basic "Restricted Content";`：开启身份验证。引号内的文本是登录弹窗中显示的消息。
3. `auth_basic_user_file ...`：告诉 Nginx 在哪里查找我们在步骤 2 中创建的文件。

这是 Nginx 基于路径行为的另一个示例。不同的 URL 位置可以有不同的安全规则，这让你能够仅保护网站中需要限制的部分。

保存文件（`Ctrl+O`，`Enter`）并退出（`Ctrl+X`）。

在重新加载之前，请务必检查是否有拼写错误：

```bash
sudo nginx -t
```

如果测试成功，重新加载 Nginx 以激活安全设置：

```bash
sudo service nginx reload
```
# 测试安全性

现在是验证我们的安全措施是否生效的时候了。

**测试 1：不使用密码访问**

尝试在不提供任何凭据的情况下访问受保护页面：

```bash
curl -I http://localhost/secure/
```

你应该会看到 `HTTP/1.1 401 Unauthorized`。

401 响应是服务器表示「在提供资源之前需要身份验证」的标准 HTTP 方式。

这意味着 Nginx 成功拦截了请求，并要求提供身份证明。

**测试 2：使用密码访问**

现在，使用 `-u` 标志（`username:password`）提供凭据。

```bash
curl -u user1:labex http://localhost/secure/
```

你应该会看到 HTML 内容：`<h1>Welcome to the Secured Area</h1>`。

这证实了：

1. 陌生人被拦截（401 Unauthorized）。
2. 授权用户被允许访问（200 OK）。
# 总结

在本实验中，你成功实现了基本身份验证以保护网站目录。你学习了如何：

1. **安装** `apache2-utils` 以获取密码生成工具。
2. **生成** 使用 `htpasswd` 的安全密码文件。
3. **配置** Nginx 使用 `auth_basic` 限制访问。
4. **验证** 使用 `curl` 状态码进行访问控制。

这为保护 Web 服务器的敏感区域提供了一种快速且有效的安全层。
