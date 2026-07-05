# 验证 Jenkins Web 端点

## 当前状况

Docker 检查证明了容器的存在，但运维人员还需要知道 Jenkins 是否通过 HTTP 正常响应。

## 范围

- Jenkins 预期运行在 `http://localhost:8080`。
- 将你的 HTTP 证据存储在 `/home/labex/project/jenkins-web-report.txt` 中。

## 你的目标

创建一个 Web 报告，记录来自 Jenkins 的成功 HTTP 响应，并确认该页面确实是 Jenkins 页面。

## 验收标准

- 报告文件存在于 `/home/labex/project` 目录下。
- 报告中包含 HTTP `200` 响应。
- 报告中包含来自本地服务的 Jenkins 页面内容。

## 提示

<details>
<summary>需要一个起点吗？</summary>

使用带有状态码格式字符串的 `curl`，然后获取 Jenkins 页面主体的一小部分内容。
</details>
