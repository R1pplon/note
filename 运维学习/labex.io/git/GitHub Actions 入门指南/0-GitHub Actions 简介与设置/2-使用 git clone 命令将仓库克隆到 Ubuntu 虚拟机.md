# 使用 git clone 命令将仓库克隆到 Ubuntu 虚拟机

在这一步，你将把刚刚创建的仓库克隆到你的本地 LabEx 环境中。这使得你可以在本地编辑文件和创建工作流。

1. 在你的 GitHub 仓库页面上，点击绿色的 **Code** 按钮。
2. 确保选择了 **HTTPS** 标签页，并复制该 URL。它看起来应该像 `https://github.com/your-username/github-actions-demo.git`。
3. 在 LabEx 环境中打开终端。默认路径是 `~/project`。
4. 使用 `git clone` 命令下载仓库。将 `your-username` 替换为你实际的 GitHub 用户名。

```bash
cd ~/project
git clone https://github.com/your-username/github-actions-demo.git
```

**示例输出：**

```plaintext
Cloning into 'github-actions-demo'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
```

5. 验证目录是否已创建：

```bash
ls -F
```

你应该会看到 `github-actions-demo/` 被列出。
