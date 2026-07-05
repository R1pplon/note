# 创建生成 JUnit XML 的任务

在此步骤中，你将创建一个编写 JUnit XML 文件的 Jenkins 自由风格任务。JUnit XML 是一种通用的报告格式，即使测试数据来自简单的 Shell 脚本，Jenkins 也能读取它。

打开桌面界面。Firefox 会自动打开 Jenkins。如果没有，请访问 `http://localhost:8080`。

在 Jenkins 仪表板中：

点击 `New Item`，输入 `test-report-demo`，选择 `Freestyle project`，然后点击 `OK`。

在配置页面，向下滚动到 `Build Steps`，点击 `Add build step`，然后选择 `Execute shell`。

在 `Command` 框中输入以下 Shell 脚本：

```bash
mkdir -p reports
printf '%s\n' \
  '<testsuite name="LabExSuite" tests="2" failures="0" skipped="0">' \
  '  <testcase classname="demo.HealthTest" name="test_home_page"/>' \
  '  <testcase classname="demo.HealthTest" name="test_api_status"/>' \
  '</testsuite>' > reports/junit.xml
echo "Generated passing JUnit report"
```

点击 `Save`。

保存后，任务页面将会打开。

![Jenkins 测试报告任务](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-publishing-test-reports-and-build-notifications/zh/../assets/step1-test-report-job.png)

在终端中运行此命令以记录已保存的 Shell 步骤：

```bash
docker exec jenkins sh -lc "grep -n -E 'reports/junit.xml|Generated passing JUnit report' /var/jenkins_home/jobs/test-report-demo/config.xml" | tee /home/labex/project/test-report-job-config.txt
```
