# 使用 umask 管理默认权限

你的任务是为当前的终端会话设置一个特定的 `umask` 值，然后创建一个新文件和目录，以验证默认权限是否已正确应用。

## 任务

- 将当前终端会话的 `umask` 值设置为 `0022`。
- 在 `~/project` 目录下，创建一个名为 `test_file` 的空文件。
- 在 `~/project` 目录下，创建一个名为 `test_dir` 的新目录。

## 要求

- 所有操作必须在 `~/project` 目录下进行。
- 使用 `umask` 命令设置权限掩码。
- 使用 `touch` 命令创建文件。
- 使用 `mkdir` 命令创建目录。

## 示例

在正确设置 `umask` 并创建文件和目录后，使用 `ls -l` 检查它们的权限，应该会产生类似于以下的输出：

```plaintext
$ ls -l ~/project
total 0
drwxr-xr-x 2 labex labex 6 Aug 19 07:43 test_dir
-rw-r--r-- 1 labex labex 0 Aug 19 07:43 test_file
```
