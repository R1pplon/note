# 识别并修改 SELinux 文件上下文

你的第一个任务是处理文件的 SELinux 上下文。你将创建一个新文件，检查其默认的 SELinux 上下文，然后修改其类型上下文。

## 任务

- 创建一个名为 `example.txt` 的空文件。
- 查看 `example.txt` 文件的完整 SELinux 上下文。
- 将 `example.txt` 的 SELinux 类型上下文更改为 `admin_home_t`。

## 要求

- 所有操作必须在 `~/project` 目录下执行。
- 文件名必须为 `example.txt`。
- 使用 `ls -Z` 命令查看文件上下文。
- 使用 `chcon` 命令修改文件上下文。最终的类型上下文必须为 `admin_home_t`。
