# 创建项目并配置 Git SCM

安装 Git 插件后，你现在可以创建一个 Jenkins 项目，并将其配置为从 Jenkins 容器中准备好的本地仓库拉取源代码。

1. 点击左上角面包屑导航中的 **Dashboard**（仪表板）链接，返回 Jenkins 主仪表板。
2. 点击左侧边栏中的 **New Item**（新建任务）。
3. 输入 `git-integration-project` 作为项目名称。
4. 选择 **Freestyle project**（自由风格项目）。
5. 点击 **OK**。

这将带你进入项目的配置页面。

6. 向下滚动到 **Source Code Management**（源码管理）部分。
7. 选择 **Git** 选项。如果你没有看到此选项，请确认 Git 插件是否在之前的步骤中正确安装。
8. 在 **Repository URL**（仓库 URL）字段中，输入为本次实验准备的本地仓库 URL：

```plaintext
git://localhost/git-playground.git
```

9. 将 **Branch Specifier**（分支指定）保留为默认值 `*/master`。这告诉 Jenkins 使用 `master` 分支。
10. 点击页面底部的 **Save**（保存）按钮。

![Git SCM 配置](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-integrating-jenkins-with-source-control-git/zh/../assets/step2-git-scm.png)

你已经成功创建了一个链接到本地 Git 仓库的 Jenkins 作业。在下一步中，你将定义作业应如何处理检出的代码。
