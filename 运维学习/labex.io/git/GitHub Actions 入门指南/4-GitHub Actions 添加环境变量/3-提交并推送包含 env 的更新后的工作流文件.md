# 提交并推送包含 env 的更新后的工作流文件

在这一步，你将提交新的工作流文件并将其推送到 GitHub。推送此文件将使工作流在 GitHub Actions 中注册，并触发第一次运行。

1. 确保你位于仓库目录中：

```bash
cd ~/project/github-actions-demo
```

2. 暂存工作流文件：

```bash
git add .github/workflows/env-demo.yml
```

3. 提交更改：

```bash
git commit -m "Add workflow with environment variable"
```

4. 将更改推送到 GitHub 上的远程仓库：

```bash
git push
```

**关于身份验证的注意事项：**
当你运行 `git push` 时，WebIDE 会自动提示你进行身份验证。请遵循以下详细步骤：

1. 会弹出一个提示框，显示消息："The extension 'GitHub' wants to sign in using GitHub."（扩展 'GitHub' 希望使用 GitHub 登录。）点击 **Allow**（允许）。
2. 会出现一个新的通知。点击 **"Copy&Continue to GitHub"**（复制并继续到 GitHub），然后在下一个提示中点击 **"Open"**（打开）。
3. 在打开的浏览器窗口中登录你的 GitHub 账户，并输入被复制的授权码。确认授权后，页面将自动关闭。
4. 等待几秒钟，你将看到终端成功完成推送操作。

**隐私声明：** WebIDE 会请求完全访问你的 GitHub 账户以进行身份验证。你无需担心隐私问题——在当前实验（lab）完成后，LabEx 虚拟机将立即销毁，你的凭证和授权信息不会被保留。
