# 提交、推送并在 GitHub UI 中验证制品 (artifact)

在这一步，你将提交更改并将其推送到 GitHub。这将触发你刚刚更新的工作流 (workflow)。

1. 确保你位于仓库目录中：

```bash
cd ~/project/github-actions-demo
```

2. 暂存更改：

```bash
git add .
```

3. 提交更改：

```bash
git commit -m "Add build step and upload artifacts"
```

4. 将更改推送到 GitHub 上的远程仓库：

```bash
git push
```

## 关于认证的说明 (Note on Authentication)

当你运行 `git push` 时，WebIDE 会自动提示你进行认证。请遵循以下详细步骤：

1. 会弹出一个提示框，显示信息："The extension 'GitHub' wants to sign in using GitHub." (扩展 'GitHub' 希望使用 GitHub 登录。) 点击 **Allow** (允许)。
2. 会出现一个新的通知。点击 **"Copy&Continue to GitHub"** (复制并继续到 GitHub)，然后在下一个提示中点击 **"Open"** (打开)。
3. 在打开的浏览器窗口中登录你的 GitHub 账户，并输入已复制的授权码。确认授权后，页面将自动关闭。
4. 等待几秒钟，你将看到终端成功完成推送操作。

**隐私声明：** WebIDE 会请求对你的 GitHub 账户的完全访问权限以进行认证。你无需担心隐私问题——在你完成当前实验 (Lab) 后，LabEx 虚拟机 (VM) 将立即销毁，你的凭证和授权信息不会被保留。

此认证过程不需要手动配置用户名或个人访问令牌 (Personal Access Token)。

## 在 GitHub 上验证 (Verify on GitHub)

1. 在网页浏览器中访问你的 GitHub 仓库。
2. 点击 **Actions** 标签页。
3. 点击最新的工作流运行记录（例如 "Add build step and upload artifacts"）。
4. 向下滚动到摘要页面的底部。你应该会看到一个标题为 **Artifacts** 的部分。
5. 你应该会看到一个名为 `build-assets` 的构件 (artifact)。
6. 点击它以下载。它将是一个包含 `build.txt` 的 zip 文件。

![GitHub Artifacts](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/git/github-actions-for-beginners/lab-github-actions-uploading-build-artifacts/zh/../assets/20251215-09-26-53-cUoWdafr.png)
