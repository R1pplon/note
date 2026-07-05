# 检查 Jenkins 运行时

## 当前情况

Jenkins 已在 Docker 中运行。在创建流水线之前，请确认运行时环境是否正常。

## 范围

- Jenkins 容器名称为 `jenkins`。
- Jenkins 应在 `http://localhost:8080/login` 响应。
- 将证据写入 `/home/labex/project/runtime-report.txt`。

## 你的目标

创建一个报告，证明 Docker 正在运行 Jenkins 容器，且 Jenkins 可以通过 HTTP 正常响应。

## 验收标准

- 报告文件已存在。
- 报告包含 `container=jenkins`。
- 报告包含 `http_status=200`。

## 提示

<details>
<summary>需要一个起点吗？</summary>

使用 `docker ps` 查看容器名称，并使用 `curl` 记录 Jenkins 登录页面的 HTTP 状态码。
</details>
