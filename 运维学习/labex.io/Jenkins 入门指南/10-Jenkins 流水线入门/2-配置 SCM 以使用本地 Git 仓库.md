# 配置 SCM 以使用本地 Git 仓库

现在，你将配置 Jenkins 项目，使其从 Jenkins 容器内托管的本地 Git 仓库中获取流水线定义。这使得实验独立于外部网络，同时仍然使用真实的「来自 SCM 的流水线脚本」（Pipeline script from SCM）工作流。

演示仓库位于：`git://localhost/pipeline-demo.git`

让我们检查一下 Jenkins 将从该仓库加载的 `Jenkinsfile`：

```groovy
pipeline {
    agent any
    environment {
        DEPLOY_TARGET = 'staging'
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building the application...'
                sh 'echo "Compiling source code..."'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing the application...'
                sh 'echo "Running unit tests..."'
            }
        }
        stage('Deploy') {
            steps {
                echo "Deploying the application to ${env.DEPLOY_TARGET}..."
                sh 'echo "Application deployed successfully!"'
            }
        }
    }
}
```

这个声明式流水线脚本包含了多个阶段，并展示了关键概念：

- `pipeline { ... }`：定义整个流水线的主块。
- `agent any`：指示 Jenkins 在任何可用的代理上运行此流水线。
- `environment { ... }`：定义可在整个流水线中使用的环境变量。
- `stages { ... }`：流水线中所有阶段的容器。
- `stage('...') { ... }`：定义特定的阶段。阶段有助于可视化工作流。
- `steps { ... }`：包含在阶段中执行的实际命令。
- `echo '...'`：将消息打印到构建日志的步骤。
- `sh '...'`：执行 Shell 命令的步骤。

现在，配置你的 Jenkins 项目以使用此仓库：

1. 返回浏览器中的 Jenkins UI，导航到 `my-first-pipeline` 项目页面。
2. 点击左侧菜单中的 **Configure**（配置）。
3. 向下滚动到 **Pipeline** 部分。
4. 将 **Definition**（定义）下拉菜单从 "Pipeline script" 更改为 **Pipeline script from SCM**。
5. 在出现的 **SCM** 下拉菜单中，选择 **Git**。
6. 在 **Repository URL**（仓库 URL）字段中，输入：`git://localhost/pipeline-demo.git`
7. 在 **Script Path**（脚本路径）字段中，输入：`Jenkinsfile`
8. 点击 **Save**（保存）。

![流水线 SCM 配置](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-introduction-to-jenkins-pipelines/zh/../assets/step2-pipeline-scm.png)

你的项目现在已设置为执行本地 Git 仓库中定义的流水线。
