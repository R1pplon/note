# 创建 Maven 风格的流水线

## 当前情况

自由风格任务展示了构建的形态。现在，创建一个包含明确的 `Validate`（验证）、`Package`（打包）和 `Archive`（归档）阶段的流水线版本。

## 范围

- 任务名称必须为 `maven-style-pipeline`。
- 流水线必须包含名为 `Validate`、`Package` 和 `Archive` 的阶段。
- 至少运行一次该流水线。

## 你的目标

创建并运行一个流水线任务，通过分阶段的自动化过程生成相同的 Maven 风格产物。

## 验收标准

- Jenkins 中存在一个名为 `maven-style-pipeline` 的流水线任务。
- 任务配置包含所需的阶段。
- 成功的构建日志中包含 `Pipeline Maven-style build completed`。

## 目标参考

构建完成后，`maven-style-pipeline` 的构建页面应显示为绿色成功状态。

![成功的 Maven 风格流水线构建](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/challenge-jenkins-integration-maven-plugin/zh/../assets/final-maven-pipeline.png)

## 提示

<details>
<summary>需要一个起点吗？</summary>

使用流水线的 `sh` 步骤来编写 `pom.xml`，创建 `target/` 目录，并生成 `target/demo-app.jar`。
</details>
