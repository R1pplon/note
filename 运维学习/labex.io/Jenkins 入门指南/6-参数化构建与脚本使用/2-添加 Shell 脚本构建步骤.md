# 添加 Shell 脚本构建步骤

在此步骤中，你将向任务的构建过程添加一个 Shell 脚本。这是执行代码编译、运行测试或部署应用程序等任务的常用方法。

1. 导航到 Jenkins 中的 `parameterized-script-job` 仪表板。
2. 点击左侧菜单中的 **Configure**（配置）。
3. 向下滚动到 **Build Steps**（构建步骤）部分。
4. 点击 **Add build step**（增加构建步骤）下拉菜单并选择 **Execute shell**（执行 shell）。
5. 在 **Command**（命令）文本区域中，输入以下脚本。该脚本将在 Jenkins 工作空间内创建一个项目目录结构并列出其内容。

```bash
echo "--- Starting Build Step ---"
echo "Current directory:"
pwd
echo "Creating project directory structure:"
mkdir -p my-app
echo "Version 1.0" > my-app/version.txt
echo "Listing files in the project directory:"
ls -la my-app
echo "--- Build Step Finished ---"
```

![执行 shell 构建步骤](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-parameterizing-builds-and-using-scripts/zh/../assets/step2-execute-shell.png)

6. 点击 **Save**（保存）以应用更改。

现在，让我们运行该任务以查看脚本的实际效果。

1. 在任务页面上，点击 **Build with Parameters**。
2. 保留默认参数值并点击 **Build**（构建）按钮。
3. 左侧的 **Build History**（构建历史）中会出现一个新的构建。点击构建编号（例如 `#1`）。
4. 点击 **Console Output**（控制台输出）以查看日志。

你应该能看到 Shell 脚本的输出，包括文件列表。

```plaintext
Started by user unknown or anonymous
Running as SYSTEM
Building in workspace /var/jenkins_home/workspace/parameterized-script-job
[parameterized-script-job] $ /bin/sh -xe /tmp/jenkins....sh
+ echo --- Starting Build Step ---
--- Starting Build Step ---
+ echo Current directory:
Current directory:
+ pwd
/var/jenkins_home/workspace/parameterized-script-job
+ echo Creating project directory structure:
Creating project directory structure:
+ mkdir -p my-app
+ echo Version 1.0
+ echo Listing files in the project directory:
Listing files in the project directory:
+ ls -la my-app
total 12
drwxr-xr-x 2 jenkins jenkins 4096 ... .
drwxr-xr-x 3 jenkins jenkins 4096 ... ..
-rw-r--r-- 1 jenkins jenkins   12 ... version.txt
+ echo --- Build Step Finished ---
--- Build Step Finished ---
Finished: SUCCESS
```

![Shell 构建步骤的控制台输出](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-parameterizing-builds-and-using-scripts/zh/../assets/step2-console-output.png)
