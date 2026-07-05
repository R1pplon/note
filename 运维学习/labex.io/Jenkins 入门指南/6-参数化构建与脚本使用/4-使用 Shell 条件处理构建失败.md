# 使用 Shell 条件处理构建失败

在此步骤中，你将直接在 Shell 脚本中模拟构建失败。这是一种在违反部署规则时使 Freestyle 任务停止的可靠方法。

## 修改脚本以模拟失败

现在，让我们修改脚本，使其在特定条件下失败。

1. 进入 `parameterized-script-job` 的 **Configure**（配置）页面。
2. 在 **Execute shell** 命令框中，将脚本替换为以下内容：

```bash
echo "Deploying to the $ENVIRONMENT environment."

if [ "$ENVIRONMENT" = "PROD" ]; then
  echo "Error: Production deployment is not allowed!"
  echo "Failure handled by shell condition."
  exit 1
fi

echo "Deployment to $ENVIRONMENT was successful."
```

该脚本会检查 `ENVIRONMENT` 参数。如果设置为 `PROD`，它将打印错误并以状态码 `1` 退出。在 Shell 脚本中，任何非零的退出码都会向 Jenkins 发出失败信号。

3. 点击 **Save**（保存）。

## 测试失败条件

现在，让我们测试失败条件。

1. 点击 **Build with Parameters**。
2. 为 `ENVIRONMENT` 选择 `PROD` 并点击 **Build**。
3. 构建将失败，构建历史记录中会显示一个红色图标。
4. 检查 **Console Output**。你将看到来自脚本的错误消息以及最终的 Jenkins 构建状态。

![显示构建失败的控制台输出](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-parameterizing-builds-and-using-scripts/zh/../assets/step4-failed-build-console.png)

```plaintext
Deploying to the PROD environment.
+ [ PROD = PROD ]
+ echo Error: Production deployment is not allowed!
Error: Production deployment is not allowed!
+ echo Failure handled by shell condition.
Failure handled by shell condition.
+ exit 1
Build step 'Execute shell' marked build as failure
Finished: FAILURE
```

如果你再次使用 `DEV` 或 `STAGING` 运行构建，它将会成功，因为失败条件未被触发。
