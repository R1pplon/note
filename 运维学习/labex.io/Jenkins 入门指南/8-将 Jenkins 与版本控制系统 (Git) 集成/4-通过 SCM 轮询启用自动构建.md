# 通过 SCM 轮询启用自动构建

手动运行构建对于测试很有用，但 CI 的真正威力在于自动化。在这一步中，你将配置 Jenkins 轮询 Git 仓库以检查更改。

1. 导航回 `git-integration-project` 的配置页面（项目页面 > **Configure**）。
2. 向下滚动到 **Build Triggers**（构建触发器）部分。
3. 勾选 **Poll SCM**（轮询 SCM）旁边的复选框。
4. 将出现一个 **Schedule**（日程表）文本框。它使用 cron 语法来定义 Jenkins 检查仓库更改的频率。要每分钟轮询一次，请输入以下内容：

```plaintext
* * * * *
```

5. 点击 **Save**（保存）。

该计划将随作业配置一起保存。Jenkins 现在将每分钟检查一次配置的仓库，并在检测到新提交时启动新的构建。

6. 返回 Jenkins UI 并进入 `git-integration-project` 页面。
7. 在左侧边栏中，你会看到一个名为 **Git Polling Log**（Git 轮询日志）的新选项。点击它。
8. 此页面显示 Jenkins 至少运行一个轮询周期后的轮询活动。第一个条目出现可能需要一分钟。你应该会看到类似以下的条目：

```plaintext
Started on ...
Using strategy: Default
[poll] Last Built Revision: Revision ...
The recommended git tool is: NONE
No credentials specified
 > git --version # timeout=10
 > git ls-remote -h -- git://localhost/git-playground.git # timeout=10
...
No changes
```

![SCM 轮询配置](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-integrating-jenkins-with-source-control-git/zh/../assets/step4-scm-polling.png)

这表明 Jenkins 已配置为自动轮询仓库。在实际项目中，仓库通常托管在 Git 服务器上，当出现新提交时，Jenkins 就会进行构建。
