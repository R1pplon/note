# 发布 JUnit 测试结果报告

在此步骤中，你将添加一个构建后操作（Post-build action）来发布 JUnit XML 文件。构建步骤在构建过程中创建文件，而构建后操作则在构建完成后处理这些文件。

在 `test-report-demo` 任务页面，点击 `Configure`。

向下滚动到 `Post-build Actions`，点击 `Add post-build action`，然后选择 `Publish JUnit test result report`。

在 `Test report XMLs` 字段中，输入：

```text
reports/junit.xml
```

点击 `Save`。

![Jenkins JUnit 发布器配置](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-publishing-test-reports-and-build-notifications/zh/../assets/step2-junit-publisher.png)

运行此命令以记录已保存的 JUnit 发布器配置：

```bash
docker exec jenkins sh -lc "grep -n -E 'JUnitResultArchiver|<testResults>reports/junit.xml</testResults>' /var/jenkins_home/jobs/test-report-demo/config.xml" | tee /home/labex/project/junit-publisher-lines.txt
```

输出应包含 `JUnitResultArchiver` 和 `reports/junit.xml`。
