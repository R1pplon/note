# 检查日志以确保 Secret 在不暴露的情况下被使用

推送代码后，工作流将自动在 GitHub 上触发。你可以查看工作流运行日志，以验证密钥是否被安全使用。

1. 在网页浏览器中访问你的仓库：`https://github.com/your-username/github-actions-demo`
2. 点击仓库页面顶部的 **Actions** 标签页。
3. 你应该会看到一个工作流运行列表，名称可能为 "Add workflow to test secrets" 或 "Secrets Demo"。

**注意：** 当你推送代码时，如果仓库中有多个工作流文件，可能会触发多个 Actions。请通过检查左侧边栏中的工作流名称或使用右侧的工作流运行列表来识别正确的工作流运行。

4. 点击工作流运行标题，然后点击 **use-secret** 任务（job）。
5. 展开 **Print Secret** 步骤以查看输出。

你应该会看到类似以下内容：

```plaintext
Printing secret directly (masked): ***
Printing secret from env (masked): ***
```

请注意，即使工作流尝试打印密钥值，GitHub Actions 也会自动在日志中用 `***` 对其进行掩码处理。这是一个关键的安全特性——它能防止敏感信息意外地暴露在日志中，即使你尝试打印它。这确认了你的密钥已安全配置并在工作流中使用，而没有暴露其真实值。

![GitHub Actions logs](https://file.labex.io/namespace/df87b950-1f37-4316-bc07-6537a1f2c481/git/github-actions-for-beginners/lab-github-actions-setting-up-secrets/zh/../assets/20251127-15-17-06-VTLe80I2.png)
