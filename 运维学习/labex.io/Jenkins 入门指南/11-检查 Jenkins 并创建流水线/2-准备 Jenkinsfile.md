# 准备 Jenkinsfile

## 当前情况

运行时环境正常。现在创建 Jenkins 将要运行的流水线定义。

## 范围

- 将项目存储在 `/home/labex/project/pipeline-demo` 下。
- 创建 `/home/labex/project/pipeline-demo/Jenkinsfile`。
- 包含名为 `Build`、`Test` 和 `Deploy` 的阶段（stage）。

## 你的目标

为一个简单的三阶段流水线准备一个 Jenkinsfile。

## 验收标准

- Jenkinsfile 已存在。
- 它包含 `Build`、`Test` 和 `Deploy` 阶段。
- 它会打印 `Hello from Jenkins Pipeline`。

## 提示

<details>
<summary>需要一个起点吗？</summary>

声明式流水线（Declarative Pipeline）使用顶层的 `pipeline` 块、`agent` 以及每个命名阶段对应的 `stage` 块。
</details>
