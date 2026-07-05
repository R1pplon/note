# 添加 Groovy 控制流

在此步骤中，你将使用 Groovy 控制流更新脚本化流水线。`if` 块决定是否运行 `Test` 阶段。`try/finally` 块确保即使某个阶段失败，清理消息也会执行。

用更丰富的功能版本替换流水线脚本：

```bash
cat > /home/labex/project/scripted-pipeline.groovy <<'GROOVY'
node {
    def runTests = true

    try {
        stage('Prepare') {
            echo 'Preparing scripted pipeline'
            sh 'pwd'
        }

        stage('Build') {
            echo 'Building with scripted syntax'
            sh 'echo Compiling scripted pipeline demo'
        }

        if (runTests) {
            stage('Test') {
                echo 'Tests enabled by Groovy condition'
                sh 'echo Running scripted pipeline tests'
            }
        }
    } finally {
        echo 'Pipeline cleanup complete'
    }
}
GROOVY
```

重新生成任务配置并重新加载 Jenkins：

```bash
python3 - <<'PY'
from html import escape
from pathlib import Path

script = Path("/home/labex/project/scripted-pipeline.groovy").read_text()
config = f"""<?xml version='1.1' encoding='UTF-8'?>
<flow-definition plugin="workflow-job">
  <actions/>
  <description>Run a Jenkins scripted Pipeline example.</description>
  <keepDependencies>false</keepDependencies>
  <properties/>
  <definition class="org.jenkinsci.plugins.workflow.cps.CpsFlowDefinition" plugin="workflow-cps">
    <script>{escape(script)}</script>
    <sandbox>true</sandbox>
  </definition>
  <triggers/>
  <disabled>false</disabled>
</flow-definition>
"""
Path("/home/labex/project/scripted-pipeline-config.xml").write_text(config)
PY
docker cp /home/labex/project/scripted-pipeline-config.xml jenkins:/var/jenkins_home/jobs/scripted-pipeline-demo/config.xml
/home/labex/project/reload-jenkins.sh
```

确认更新后的任务配置包含新的 `Test` 阶段文本：

```bash
docker exec jenkins grep -n 'Tests enabled by Groovy condition' /var/jenkins_home/jobs/scripted-pipeline-demo/config.xml
```
