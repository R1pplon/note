# 在 GitHub 设置中创建一个名为 MY_SECRET 的仓库 Secret

在此步骤中，你将在 GitHub 上的现有 `github-actions-demo` 仓库中添加一个 Secret，并将该仓库克隆到你的 LabEx 环境中。

## 添加仓库 Secret

1. 打开你的网页浏览器，导航到 GitHub 上的 `github-actions-demo` 仓库：`https://github.com/your-username/github-actions-demo`
2. 点击 **Settings** 标签页。
3. 在左侧边栏中，向下滚动到 **Security** 部分，点击 **Secrets and variables**，然后选择 **Actions**。
4. 点击绿色的 **New repository secret** 按钮。
5. 输入以下详细信息：
    - **Name**: `MY_SECRET`
    - **Secret**: `SuperSecretValue123`
6. 点击 **Add secret**。

![GitHub Actions secrets](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/git/github-actions-for-beginners/lab-github-actions-setting-up-secrets/zh/../assets/20251127-15-14-53-altx62pa.png)

现在，你应该在 "Repository secrets" 下方看到 `MY_SECRET` 列表。

## 克隆仓库

现在，切换回 LabEx 环境以克隆仓库。

1. 在你的 `github-actions-demo` GitHub 仓库页面上，点击绿色的 **Code** 按钮。
2. 确保选择了 **HTTPS** 标签页，并复制该 URL。它应该看起来像 `https://github.com/your-username/github-actions-demo.git`。
3. 打开 LabEx 环境中的终端。默认路径是 `~/project`。
4. 使用 `git clone` 命令下载仓库。将 `your-username` 替换为你的实际 GitHub 用户名。

```bash
cd ~/project
git clone https://github.com/your-username/github-actions-demo.git
```

**示例输出：**

```plaintext
Cloning into 'github-actions-demo'...
remote: Enumerating objects: X, done.
remote: Counting objects: 100% (X/X), done.
remote: Total X (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (X/X), done.
```

5. 进入克隆的仓库目录：

```bash
cd ~/project/github-actions-demo
```
