# 创建并配置 Bash 脚本

让我们创建一个 Bash 脚本，用于处理并显示传递给它的参数信息。

## 任务

- 在 `/home/labex/` 目录下创建一个名为 `argts.sh` 的 Bash 脚本
- 为该脚本添加可执行权限
- 实现脚本功能以显示以下信息：
  - 提供的参数总数
  - 第一个参数的值
  - 脚本的进程 ID (PID)
  - 所有提供的参数

## 要求

- 脚本必须创建在 `/home/labex/argts.sh`
- 脚本必须包含正确的 Shebang 行：`#!/bin/bash`
- 脚本必须具有可执行权限
- 脚本必须使用适当的 Bash 特殊变量来显示所需信息
- 输出应使用等号组成的标题行进行清晰的格式化

## 示例

当运行带有参数的脚本时，它应该产生类似于以下的输出：

```
$ /home/labex/argts.sh one two three
===========================
The total count of the supp argts: 3
The value of the first arg is: one
The PID of the script is : 12345
All the argts: one two three
```
