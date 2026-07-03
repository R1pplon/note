# 提交并推送包含 Secret 引用的工作流

在这一步，你将提交你的更改并将其推送到 GitHub。这将触发 GitHub Action。

1. 确保你位于仓库目录中：

```bash
cd ~/project/github-actions-demo
```

2. 暂存工作流文件：

```bash
git add .github/workflows/secrets-demo.yml
```

3. 使用描述性的消息提交更改：

```bash
git commit -m "Add workflow to test secrets"
```

4. 将更改推送到 GitHub 上的远程仓库：

```bash
git push
```

**关于认证的注意事项：**
当你运行 `git push` 时，WebIDE 会自动提示你进行认证。请遵循以下详细步骤：

1. 会弹出一个提示框，显示消息：“The extension 'GitHub' wants to sign in using GitHub.”点击 **Allow**。
2. 会出现一个新的通知。点击 **"Copy&Continue to GitHub"**，然后在下一个提示中点击 **"Open"**。
3. 在打开的浏览器窗口中登录你的 GitHub 账户，并输入已复制的授权码。确认授权后，页面将自动关闭。
4. 等待几秒钟，你将看到终端成功完成推送操作。

**隐私声明：** WebIDE 会请求完全访问你的 GitHub 账户以进行认证。你无需担心隐私问题——在当前实验（lab）完成后，LabEx VM 将立即销毁，你的凭证和授权信息将不会被保留。
