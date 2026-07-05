# 创建 Jenkins 视图

## 当前情况

Jenkins 仪表板目前是空的。你需要创建视图，将开发工作与个人视图区分开来。

## 范围

- Jenkins 可通过 `http://localhost:8080` 访问。
- 创建一个名为 `dev-helloworld` 的自由风格（Freestyle）任务。
- 创建一个名为 `dev` 的列表视图（List View）。
- 创建一个名为 `test` 的个人视图（My View）。

## 你的目标

创建所需的任务以及这两个 Jenkins 视图。

## 验收标准

- 任务 `dev-helloworld` 存在。
- `dev` 视图存在，且类型为列表视图。
- `test` 视图存在。

## 提示

<details>
<summary>需要一个起点吗？</summary>

你可以使用 Jenkins UI 或 Jenkins `scriptText` 来创建任务和视图。
</details>
