# 创建并参数化 Freestyle 项目

在此步骤中，你将创建一个新的 Jenkins Freestyle 项目并为其添加参数。参数使你的任务具有交互性，允许你在每次触发构建时提供输入。

首先，让我们创建任务。

1. 从桌面界面打开 Firefox 浏览器。浏览器会自动打开 `http://localhost:8080`，无需手动输入 URL。无需登录。
2. 在 Jenkins 仪表板上，点击左侧菜单中的 **New Item**（新建任务）。
3. 输入 `parameterized-script-job` 作为任务名称。
4. 选择 **Freestyle project** 并点击 **OK**。

![创建新的 Freestyle 项目](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-parameterizing-builds-and-using-scripts/zh/../assets/step1-create-job.png)

你将被带到任务的配置页面。现在，让我们添加参数。

1. 在 **General**（常规）部分，勾选 **This project is parameterized**（此项目是参数化的）。

   ![启用任务参数](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-parameterizing-builds-and-using-scripts/zh/../assets/step1-enable-parameters.png)

2. 点击 **Add Parameter**（添加参数）下拉菜单并选择 **String Parameter**（字符串参数）。
   - **Name**: `GREETING_NAME`
   - **Default Value**: `World`
   - **Description**: `Enter a name to be greeted.`
3. 再次点击 **Add Parameter** 下拉菜单并选择 **Choice Parameter**（选项参数）。

   - **Name**: `ENVIRONMENT`
   - **Choices**: (每行输入一个选项)

     ```
     DEV
     STAGING
     PROD
     ```

   - **Description**: `Select the deployment environment.`

这些参数将在后续的构建步骤中使用。

![配置字符串和选项参数](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-parameterizing-builds-and-using-scripts/zh/../assets/step1-configured-parameters.png)

最后，滚动到底部并点击 **Save**（保存）。你将被重定向到任务的主页面，现在你会看到 **Build with Parameters**（参数化构建）选项，而不是“Build Now”（立即构建）。

![任务页面上的参数化构建链接](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-parameterizing-builds-and-using-scripts/zh/../assets/step1-build-with-parameters.png)
