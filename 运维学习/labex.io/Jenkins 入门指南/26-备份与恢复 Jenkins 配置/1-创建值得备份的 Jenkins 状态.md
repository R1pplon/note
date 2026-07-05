# 创建值得备份的 Jenkins 状态

在此步骤中，你将创建一个名为 `backup-restore-demo` 的自由风格（Freestyle）任务。这将为你提供一个真实的 Jenkins 配置文件，以便进行备份和恢复练习。

编写任务配置：

```bash
cat > /home/labex/project/backup-restore-demo-config.xml <<'XML'
<?xml version='1.1' encoding='UTF-8'?>
<project>
  <actions/>
  <description>Job used to practice Jenkins backup and restore.</description>
  <keepDependencies>false</keepDependencies>
  <properties/>
  <scm class="hudson.scm.NullSCM"/>
  <canRoam>true</canRoam>
  <disabled>false</disabled>
  <blockBuildWhenDownstreamBuilding>false</blockBuildWhenDownstreamBuilding>
  <blockBuildWhenUpstreamBuilding>false</blockBuildWhenUpstreamBuilding>
  <triggers/>
  <concurrentBuild>false</concurrentBuild>
  <builders>
    <hudson.tasks.Shell>
      <command>echo "Restored Jenkins job is running"</command>
      <configuredLocalRules/>
    </hudson.tasks.Shell>
  </builders>
  <publishers/>
  <buildWrappers/>
</project>
XML
```

将任务复制到 Jenkins 并重载：

```bash
docker exec -u root jenkins mkdir -p /var/jenkins_home/jobs/backup-restore-demo
docker cp /home/labex/project/backup-restore-demo-config.xml jenkins:/var/jenkins_home/jobs/backup-restore-demo/config.xml
docker exec -u root jenkins chown -R jenkins:jenkins /var/jenkins_home/jobs/backup-restore-demo
/home/labex/project/reload-jenkins.sh
```

确认 Jenkins 可以加载该任务：

```bash
curl -fsS http://localhost:8080/job/backup-restore-demo/api/json | grep -o '"name":"backup-restore-demo"'
```
