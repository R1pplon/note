# 使用 git add、git commit 和 git push 命令提交并推送目录更改

在此步骤中，你将提交新的目录结构并将其推送到 GitHub。

1. 暂存（Stage）新文件以供提交：

```bash
git add .
```

2. 使用描述性的消息提交更改：

```bash
git commit -m "Setup GitHub Actions workflow directory"
```

**示例输出：**

```plaintext
[main ... ] Setup GitHub Actions workflow directory
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 .github/workflows/main.yml
```

3. 将更改推送到 GitHub 上的远程仓库：

```bash
git push
```

**关于身份验证的注意事项：**
当你运行 `git push` 时，WebIDE 会自动提示你进行身份验证。请遵循以下详细步骤：

1. 将出现一个弹出窗口，显示消息：“The extension 'GitHub' wants to sign in using GitHub.”（扩展程序 'GitHub' 希望使用 GitHub 登录。）点击 **Allow**（允许）。
2. 将出现一个新的通知。点击 **"Copy&Continue to GitHub"**（复制并继续到 GitHub），然后在下一个提示中点击 **"Open"**（打开）。
3. 在打开的浏览器窗口中登录你的 GitHub 帐户，并输入已复制的授权码。确认授权后，页面将自动关闭。
4. 等待几秒钟，你将看到终端成功完成推送操作。

![GitHub Authentication](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/git/github-actions-for-beginners/lab-github-actions-introduction-and-setup/zh/../assets/20251127-11-15-38-jKrAX1S7.png)

**隐私声明：** WebIDE 会请求对你的 GitHub 帐户的完全访问权限以进行身份验证。你无需担心隐私问题——在完成当前实验（lab）后，LabEx 虚拟机将立即销毁，你的凭据和授权信息不会被保留。

此身份验证过程不需要手动配置用户名或 Personal Access Token（个人访问令牌）。

遵循身份验证步骤后，你将看到类似以下内容的输出：

**示例输出：**

```plaintext
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 4 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (5/5), 388 bytes | 388.00 KiB/s, done.
Total 5 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/your-username/github-actions-demo.git
   2e0917f..a191a97  master -> master
```

4. 在 Web 浏览器中访问你的 GitHub 仓库，确认 `.github/workflows` 目录和 `main.yml` 文件现在已在仓库结构中可见。
