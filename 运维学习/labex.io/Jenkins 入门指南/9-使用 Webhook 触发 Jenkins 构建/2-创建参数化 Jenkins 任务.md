# 创建参数化 Jenkins 任务

在此步骤中，你将创建一个名为 `webhook-trigger-demo` 的 Jenkins 自由风格任务。该任务将接收两个字符串参数：`WEBHOOK_BRANCH` 和 `WEBHOOK_COMMIT`。这模拟了源代码控制 Webhook 发送的关键数据。

创建 Jenkins 任务的 XML 配置文件。任务中的 Shell 命令将打印分支和提交信息，并检查该提交是否存在于本地 Git 仓库中。

```bash
cat > /home/labex/project/webhook-trigger-demo-config.xml <<'XML'
<?xml version='1.1' encoding='UTF-8'?>
<project>
  <actions/>
  <description>Build triggered by a local webhook payload.</description>
  <keepDependencies>false</keepDependencies>
  <properties>
    <hudson.model.ParametersDefinitionProperty>
      <parameterDefinitions>
        <hudson.model.StringParameterDefinition>
          <name>WEBHOOK_BRANCH</name>
          <description>Branch name from the webhook payload.</description>
          <defaultValue>main</defaultValue>
          <trim>true</trim>
        </hudson.model.StringParameterDefinition>
        <hudson.model.StringParameterDefinition>
          <name>WEBHOOK_COMMIT</name>
          <description>Commit id from the webhook payload.</description>
          <defaultValue></defaultValue>
          <trim>true</trim>
        </hudson.model.StringParameterDefinition>
      </parameterDefinitions>
    </hudson.model.ParametersDefinitionProperty>
  </properties>
  <scm class="hudson.scm.NullSCM"/>
  <canRoam>true</canRoam>
  <disabled>false</disabled>
  <blockBuildWhenDownstreamBuilding>false</blockBuildWhenDownstreamBuilding>
  <blockBuildWhenUpstreamBuilding>false</blockBuildWhenUpstreamBuilding>
  <triggers/>
  <concurrentBuild>false</concurrentBuild>
  <builders>
    <hudson.tasks.Shell>
      <command>set -e
echo "Webhook branch: $WEBHOOK_BRANCH"
echo "Webhook commit: $WEBHOOK_COMMIT"
echo "Source repository: /var/jenkins_home/webhook-repo"
git -C /var/jenkins_home/webhook-repo rev-parse --verify "$WEBHOOK_COMMIT^{commit}"
git -C /var/jenkins_home/webhook-repo show --no-patch --format='Commit subject: %s' "$WEBHOOK_COMMIT"</command>
      <configuredLocalRules/>
    </hudson.tasks.Shell>
  </builders>
  <publishers/>
  <buildWrappers/>
</project>
XML
```

将任务复制到 Jenkins 中，并使用设置过程中创建的辅助脚本重新加载控制器：

```bash
docker exec jenkins mkdir -p /var/jenkins_home/jobs/webhook-trigger-demo
docker cp /home/labex/project/webhook-trigger-demo-config.xml jenkins:/var/jenkins_home/jobs/webhook-trigger-demo/config.xml
/home/labex/project/reload-jenkins.sh
```

确认 Jenkins 可以识别该任务：

```bash
curl -fsS http://localhost:8080/job/webhook-trigger-demo/api/json | grep -o '"name":"webhook-trigger-demo"'
```

预期输出为：

```plaintext
"name":"webhook-trigger-demo"
```
