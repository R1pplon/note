# 创建 npm 风格的流水线

## 当前情况

自由风格任务运行正常。现在，请使用具有清晰阶段（Stage）的流水线（Pipeline）来表达相同的工作流。

## 范围

- 流水线任务名称必须为 `npm-style-pipeline`。
- 流水线必须包含名为 `Install`、`Test` 和 `Package` 的阶段。
- 至少运行一次该流水线。

## 目标

创建并运行一个流水线任务，模拟 npm 安装、测试和打包的生命周期。

## 验收标准

- Jenkins 中存在一个名为 `npm-style-pipeline` 的任务。
- 任务配置包含 `Install`、`Test` 和 `Package` 阶段。
- 流水线工作区包含 `dist/npm-style-app.tgz`。
- 构建日志包含 `Pipeline npm-style package completed`。

## 目标参考

完成的构建应在 `npm-style-pipeline` 页面显示绿色的成功状态。

![成功的 npm 风格流水线构建](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/challenge-jenkins-integration-npm-plugin/zh/../assets/final-npm-pipeline.png)

## 提示

<details>
<summary>需要一个起点吗？</summary>

使用流水线的 `sh` 步骤来创建与自由风格任务相同的文件。
</details>
