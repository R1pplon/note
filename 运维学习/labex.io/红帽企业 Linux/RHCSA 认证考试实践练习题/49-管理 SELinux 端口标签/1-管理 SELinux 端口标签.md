# 管理 SELinux 端口标签

假设你是一名系统管理员，需要配置一个 Web 服务器运行在非标准端口 `8081` 上。默认情况下，SELinux 会阻止 Web 服务器进程（例如 `httpd`）绑定到此端口，因为它缺少正确的 SELinux 标签。你的任务是使用 `semanage` 工具临时为端口 `8081` 添加正确的标签，然后再将其移除。

## 任务

- 查看与 HTTP 服务关联的现有 SELinux 端口标签。
- 添加一条新规则，将 TCP 端口 `8081` 标记为 `http_port_t` 类型，从而允许 Web 服务器使用该端口。
- 在确认新规则生效后，删除针对 TCP 端口 `8081` 的自定义规则以还原更改。

## 要求

- 所有命令必须以 `labex` 用户身份执行。在需要管理权限的地方使用 `sudo`。
- 所有操作必须在 `/home/labex` 目录下进行。
- 你必须使用 `semanage` 命令来管理 SELinux 端口标签。

## 示例

在添加新的端口标签之前，查询 `http_port_t` 将显示默认端口：

```plaintext
# sudo semanage port -l | grep http_port_t
http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
pegasus_http_port_t            tcp      5988
```

为你添加端口 `8081` 的规则后，输出应包含该新端口：

```plaintext
# sudo semanage port -l | grep http_port_t
http_port_t                    tcp      8081, 80, 81, 443, 488, 8008, 8009, 8443, 9000
pegasus_http_port_t            tcp      5988
```
