# 将参数传递给 Shell 脚本

在此步骤中，你将学习如何在 Shell 脚本中使用在步骤 1 中定义的参数。Jenkins 将构建参数公开为环境变量，你可以在脚本中使用 `$VARIABLE_NAME` 语法访问它们。

1. 返回 `parameterized-script-job` 的 **Configure**（配置）页面。
2. 向下滚动到 **Build Steps** 部分，找到 **Execute shell** 命令框。
3. 将现有脚本替换为以下内容：

```bash
echo "--- Starting Parameterized Build Step ---"
echo "Hello, $GREETING_NAME!"
echo "Deploying to the $ENVIRONMENT environment."

# Create project structure if not exists and read the version from the file
mkdir -p my-app
echo "Version 1.0" > my-app/version.txt
APP_VERSION=$(cat my-app/version.txt)
echo "Application version is: $APP_VERSION"
echo "--- Parameterized Build Step Finished ---"
```

注意 `$GREETING_NAME` 和 `$ENVIRONMENT` 是如何直接在 `echo` 命令中使用的。当任务运行时，Jenkins 会将它们替换为用户选择的值。

4. 点击 **Save**（保存）。

现在，让我们使用自定义参数运行构建。

1. 点击 **Build with Parameters**。
2. 将 `GREETING_NAME` 修改为 `LabEx`。
3. 从 `ENVIRONMENT` 下拉菜单中选择 `STAGING`。
4. 点击 **Build**。
5. 检查新构建的 **Console Output**。

输出现在应该反映你提供的自定义值。

![显示自定义参数值的控制台输出](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-parameterizing-builds-and-using-scripts/zh/../assets/step3-parameters-console.png)

```plaintext
--- Starting Parameterized Build Step ---
+ echo Hello, LabEx!
Hello, LabEx!
+ echo Deploying to the STAGING environment.
Deploying to the STAGING environment.
+ mkdir -p my-app
+ echo Version 1.0
+ cat my-app/version.txt
+ APP_VERSION=Version 1.0
+ echo Application version is: Version 1.0
Application version is: Version 1.0
+ echo --- Parameterized Build Step Finished ---
--- Parameterized Build Step Finished ---
```
