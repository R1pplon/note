# 创建 npm 风格的项目任务

## 当前情况

目前还没有 JavaScript 任务。请从创建一个能生成 npm 风格 `package.json` 的 Jenkins 自由风格任务开始。

## 范围

- 任务名称必须为 `npm-style-project`。
- 任务必须在其工作区中创建 `package.json`。
- 至少运行一次该任务。

## 目标

创建并运行一个 Jenkins 任务，用于准备一个最小化的 npm 风格项目清单。

## 验收标准

- Jenkins 中存在一个名为 `npm-style-project` 的任务。
- 工作区中包含 `package.json`。
- 构建日志中包含 `Created npm-style manifest`。

## 提示

<details>
<summary>需要一个起点吗？</summary>

使用 Execute shell 构建步骤将 JSON 内容写入 `package.json`。
</details>
