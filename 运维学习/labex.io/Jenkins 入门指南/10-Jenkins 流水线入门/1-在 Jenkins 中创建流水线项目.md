# 在 Jenkins 中创建流水线项目

第一步是在 Jenkins 中创建一个新的流水线项目。这种项目类型专门用于执行在 `Jenkinsfile` 中定义的流水线。

1. 从桌面界面打开 Firefox 浏览器。浏览器会自动打开 `http://localhost:8080`，因此无需手动输入 URL。无需登录。
2. 在 Jenkins 仪表板上，点击左侧菜单中的 **New Item**（新建任务）。
3. 在 **Enter an item name**（输入任务名称）字段中，输入 `my-first-pipeline`。
4. 从项目类型列表中选择 **Pipeline**。
5. 点击页面底部的 **OK** 按钮。

![创建流水线项目](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-introduction-to-jenkins-pipelines/zh/../assets/step1-create-pipeline.png)

6. 你将被带到项目的配置页面。目前，保持设置不变。滚动到页面底部并点击 **Save**（保存）。

你现在已经创建了一个空的流水线项目。在下一步中，你将创建一个 `Jenkinsfile` 并配置此项目来使用它。
