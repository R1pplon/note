# 定位并列出 Setuid 文件

在此步骤中，你将使用 `find` 命令搜索整个文件系统以查找 setuid 文件，并将结果保存到文件中。

## 任务

- 在整个文件系统中搜索设置了 setuid 权限的文件。
- 将 setuid 文件列表保存到你主目录中名为 `setuid_list` 的文件中。

## 要求

- 以 `labex` 用户身份在 `/home/labex` 目录下执行所有命令。
- 使用 `find` 命令搜索 setuid 文件。
- 将输出保存到 `/home/labex` 目录下的 `setuid_list` 文件中。

## 示例

完成此任务后，`setuid_list` 文件可能包含类似以下的内容：

```
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/chage
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/su
/usr/bin/mount
/usr/bin/umount
/usr/bin/crontab
/usr/bin/pkexec
```

请注意，实际列表可能会根据系统配置而有所不同。
