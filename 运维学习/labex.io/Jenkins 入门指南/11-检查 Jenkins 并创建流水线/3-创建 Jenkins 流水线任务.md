# 创建 Jenkins 流水线任务

## 当前情况

Jenkinsfile 已准备就绪。现在在控制器上创建一个流水线任务。

## 范围

- 任务名称必须为 `install-create-pipeline-demo`。
- 任务配置必须使用你 Jenkinsfile 中的流水线脚本。

## 你的目标

创建一个运行所准备脚本的 Jenkins 流水线任务。

## 验收标准

- Jenkins 中存在一个名为 `install-create-pipeline-demo` 的任务。
- 任务配置包含预期的三个阶段。

## 提示

<details>
<summary>需要一个起点吗？</summary>

你可以通过 Jenkins UI 创建流水线，或者在 `/var/jenkins_home/jobs/install-create-pipeline-demo` 下编写流水线任务的 `config.xml`。
</details>
