# 准备一个包含两个分支的 Git 仓库

在此步骤中，你将准备一个可供 Jenkins 扫描的小型 Git 仓库。该仓库位于 Jenkins 容器内部，因此 Jenkins 控制器无需依赖 GitHub 或其他外部服务即可访问它。

在终端中运行以下命令，创建一个包含 `Jenkinsfile` 的 `main` 分支：

```bash
docker exec jenkins bash -lc '
set -e
rm -rf /var/jenkins_home/multibranch-src /var/jenkins_home/multibranch-demo.git
mkdir -p /var/jenkins_home/multibranch-src
cd /var/jenkins_home/multibranch-src
git init -b main
git config user.email "jenkins@example.com"
git config user.name "Jenkins Multibranch"
cat > Jenkinsfile <<'"'"'EOF'"'"'
pipeline {
    agent any
    stages {
        stage("Branch Info") {
            steps {
                echo "Running main branch pipeline"
                sh "echo BRANCH_NAME=${BRANCH_NAME}"
            }
        }
    }
}
EOF
git add Jenkinsfile
git commit -m "Add main branch Jenkinsfile"
'
```

现在创建一个带有不同流水线消息的 `feature/demo` 分支，然后将两个分支发布到裸仓库（bare repository）中：

```bash
docker exec jenkins bash -lc '
set -e
cd /var/jenkins_home/multibranch-src
git checkout -b feature/demo
sed -i "s/Running main branch pipeline/Running feature demo pipeline/" Jenkinsfile
git add Jenkinsfile
git commit -m "Customize feature branch Jenkinsfile"
git init --bare --initial-branch=main /var/jenkins_home/multibranch-demo.git
git remote add origin /var/jenkins_home/multibranch-demo.git
git push origin main
git push origin feature/demo
touch /var/jenkins_home/multibranch-demo.git/git-daemon-export-ok
if [ -f /tmp/multibranch-git-daemon.pid ]; then
  kill "$(cat /tmp/multibranch-git-daemon.pid)" 2>/dev/null || true
  rm -f /tmp/multibranch-git-daemon.pid
fi
git daemon --reuseaddr --base-path=/var/jenkins_home --export-all --detach --pid-file=/tmp/multibranch-git-daemon.pid /var/jenkins_home
'
```

记录 Jenkins 将要扫描的分支：

```bash
docker exec jenkins git --git-dir=/var/jenkins_home/multibranch-demo.git branch -a | tee /home/labex/project/multibranch-branches.txt
```

你应该能看到两个分支：

```text
  feature/demo
* main
```
