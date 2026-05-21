# 使用 chmod (八进制模式) 更改文件权限

本步骤将教你如何使用 `chmod` 命令的八进制 (数字) 模式更改文件权限。八进制模式是一种简洁的权限表示方式，其中每个权限 (读取、写入、执行) 都分配一个数值。

权限的数值如下：

- **读取 (`r`):** 4
- **写入 (`w`):** 2
- **执行 (`x`):** 1
- **无权限 (`-`):** 0

要确定一组权限 (用户、组或其他用户) 的八进制值，你需要将授予的权限的数值相加。

例如：

- `rwx` (读取、写入、执行) = 4 + 2 + 1 = 7
- `rw-` (读取、写入、无执行) = 4 + 2 + 0 = 6
- `r-x` (读取、无写入、执行) = 4 + 0 + 1 = 5
- `r--` (读取、无写入、无执行) = 4 + 0 + 0 = 4
- `---` (无权限) = 0 + 0 + 0 = 0

`chmod` 命令的八进制模式使用三位数字，每位数字分别代表所有者、组和其他用户的权限。语法为：`chmod OGO FILE`

- **O:** 所有者权限的八进制值。
- **G:** 组权限的八进制值。
- **O:** 其他用户权限的八进制值。

让我们继续使用 `~/project/my_files/document.txt` 和 `~/project/my_files`。

首先，让我们将 `document.txt` 的权限设置为 `rw-r--r--`。

- 所有者：`rw-` = 6
- 组：`r--` = 4
- 其他：`r--` = 4

因此，八进制值为 `644`。

```bash
chmod 644 ~/project/my_files/document.txt
```

验证更改：

```bash
ls -l ~/project/my_files/document.txt
```

输出应为：

```plaintext
-rw-r--r-- 1 labex labex 0 Jun  6 00:48 /home/labex/project/my_files/document.txt
```

接下来，让我们只允许所有者执行 `document.txt`，同时保持所有者读写权限，组和其他用户只读权限。这意味着所有者将拥有 `rwx` (7)，组拥有 `r--` (4)，其他用户拥有 `r--` (4)。八进制值为 `744`。

```bash
chmod 744 ~/project/my_files/document.txt
```

验证更改：

```bash
ls -l ~/project/my_files/document.txt
```

输出应为：

```plaintext
-rwxr--r-- 1 labex labex 0 Jun  6 00:48 /home/labex/project/my_files/document.txt
```

现在，让我们更改 `~/project/my_files` 目录的权限。其当前权限为 `drwxr-xr-x`。让我们将其权限设置为 `rwxr-x---`。

- 所有者：`rwx` = 7
- 组：`r-x` = 5
- 其他：`---` = 0

因此，八进制值为 `750`。

```bash
chmod 750 ~/project/my_files
```

验证更改：

```bash
ls -ld ~/project/my_files
```

输出应显示：

```plaintext
drwxr-x--- 2 labex labex 26 Jun  6 00:48 /home/labex/project/my_files
```

这意味着所有者 (`labex`) 拥有全部权限 (读取、写入、执行)，组 (`labex`) 可以读取和执行 (进入) 目录，其他用户没有任何权限。

最后，让我们创建一个新的可执行脚本文件，以演示直接设置执行权限。

```bash
echo '#!/bin/bash' > ~/project/my_script.sh
echo 'echo "Hello from my script!"' >> ~/project/my_script.sh
```

默认情况下，新文件不可执行。让我们检查其权限：

```bash
ls -l ~/project/my_script.sh
```

你可能会看到类似 `-rw-r--r--` 的权限。为了使所有者和组可执行，但其他用户不可执行，我们需要 `rwxrwx---`。

- 所有者：`rwx` = 7
- 组：`rwx` = 7
- 其他：`---` = 0

因此，八进制值为 `770`。

```bash
chmod 770 ~/project/my_script.sh
```

验证更改：

```bash
ls -l ~/project/my_script.sh
```

输出应为：

```plaintext
-rwxrwx--- 1 labex labex 41 Jun  6 00:52 /home/labex/project/my_script.sh
```

现在你可以执行该脚本：

```bash
~/project/my_script.sh
```

你应该看到输出：

```plaintext
Hello from my script!
```
