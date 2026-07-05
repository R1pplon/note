# 创建 Maven 风格的自由风格任务

## 当前情况

Jenkins 控制器已就绪，但目前尚无 Java 构建任务。自由风格任务应创建一个小型的 Maven 风格项目结构并生成构建产物。

## 范围

- 任务名称必须为 `maven-style-freestyle`。
- 任务必须创建 `pom.xml`。
- 任务必须创建 `target/demo-app.jar`。
- 至少运行一次该任务。

## 你的目标

创建并运行一个能够生成 Maven 风格项目输出的自由风格任务。

## 验收标准

- Jenkins 中存在一个名为 `maven-style-freestyle` 的任务。
- 任务配置能够创建 `pom.xml` 和 `target/demo-app.jar`。
- 成功的构建日志中包含 `Maven-style build completed`。

## 提示

<details>
<summary>需要一个起点吗？</summary>

使用「执行 shell」构建步骤来创建项目文件和 `target` 目录。
</details>
