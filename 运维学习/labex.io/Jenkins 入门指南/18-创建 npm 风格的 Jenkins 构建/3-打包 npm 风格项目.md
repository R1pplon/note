# 打包 npm 风格项目

## 当前情况

该任务已模拟了安装和测试。现在添加一个打包输出，以便下游任务可以使用构建制品。

## 范围

- 继续使用 `npm-style-project`。
- 创建 `dist/npm-style-app.tgz`。
- 再次运行该任务。

## 目标

更新并运行任务，使其生成打包后的制品。

## 验收标准

- 任务配置创建了 `dist/npm-style-app.tgz`。
- 工作区包含 `dist/npm-style-app.tgz`。
- 最近的构建日志包含 `npm-style package completed`。

## 提示

<details>
<summary>需要一个起点吗？</summary>

使用 `mkdir -p dist` 并将文本重定向到 `.tgz` 占位符制品中。
</details>
