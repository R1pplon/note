# 使用 chmod (符号模式) 更改文件权限

本步骤将教你如何使用 `chmod` 命令的符号模式更改文件权限。符号模式使用字母和符号表示权限更改，使其直观地添加、删除或设置特定权限。

`chmod` 命令的符号模式语法为：`chmod WHO OPERATION PERMISSIONS FILE`

- **WHO:** 指定权限更改应用于谁。
  - `u`: 用户 (所有者)
  - `g`: 组
  - `o`: 其他用户
  - `a`: 所有用户 (用户、组和其他用户)
- **OPERATION:** 指定如何修改权限。
  - `+`: 添加权限。
  - `-`: 删除权限。
  - `=`: 将权限精确设置为指定的值，覆盖现有权限。
- **PERMISSIONS:** 指定权限类型。
  - `r`: 读取
  - `w`: 写入
  - `x`: 执行

让我们继续使用前面步骤中创建的 `~/project/my_files/document.txt` 文件和 `~/project/my_files` 目录。

首先，让我们从 `document.txt` 中删除组和其他用户的写入权限。回想一下，其当前权限为 `-rw-rw-r--`。

```bash
chmod go-w ~/project/my_files/document.txt
```

现在，使用 `ls -l` 验证更改：

```bash
ls -l ~/project/my_files/document.txt
```

输出应显示：

```plaintext
-rw-r--r-- 1 labex labex 0 Jun  6 17:36 /home/labex/project/my_files/document.txt
```

请注意，组和其他用户的写入权限 (`w`) 已被删除。

接下来，让我们为所有者 (`u`) 添加 `document.txt` 的执行权限。这通常用于脚本，使其可执行。

```bash
chmod u+x ~/project/my_files/document.txt
```

验证更改：

```bash
ls -l ~/project/my_files/document.txt
```

输出应为：

```plaintext
-rwxr--r-- 1 labex labex 0 Jun  6 17:36 /home/labex/project/my_files/document.txt
```

所有者现在具有执行权限 (`x`)。

现在，让我们练习使用 `~/project/my_files` 目录。其当前权限为 `drwxrwxr-x`。让我们从该目录中删除其他用户的写入权限 (`o`)。

```bash
chmod o-w ~/project/my_files
```

验证更改：

```bash
ls -ld ~/project/my_files
```

输出应显示：

```plaintext
drwxr-xr-x 2 labex labex 4096 Jun  6 17:36 /home/labex/project/my_files
```

等等，为什么 `o-w` 没有改变输出？这是因为其他用户 (`o`) 已经没有写入权限。`r-x` 表示其他用户有读取和执行权限，但没有写入权限。这表明 `chmod` 仅在更改与当前状态不同时才应用更改。

让我们尝试精确设置权限。我们将 `document.txt` 的权限设置为 `rw-r--r--` (所有者、组、其他)。这意味着所有者获得读写权限，组获得读取权限，其他用户获得读取权限。

```bash
chmod a=rw,g=r,o=r ~/project/my_files/document.txt
```

验证更改：

```bash
ls -l ~/project/my_files/document.txt
```

输出应为：

```plaintext
-rw-r--r-- 1 labex labex 0 Jun  6 17:36 /home/labex/project/my_files/document.txt
```

此命令 `a=rw,g=r,o=r` 稍微冗余，因为 `a=rw` 会将 `rw` 应用于所有用户，然后 `g=r` 会将组设置为 `r`（覆盖来自 `a=rw` 的 `w`），而 `o=r` 会将其他用户设置为 `r`（覆盖来自 `a=rw` 的 `w`）。更简单的方法是 `chmod u=rw,go=r`。让我们尝试一下。

```bash
chmod u=rw,go=r ~/project/my_files/document.txt
```

验证更改：

```bash
ls -l ~/project/my_files/document.txt
```

输出仍然为：

```plaintext
-rw-r--r-- 1 labex labex 0 Jun  6 17:36 /home/labex/project/my_files/document.txt
```

最后，让我们使 `document.txt` 对所有人可执行。

```bash
chmod a+x ~/project/my_files/document.txt
```

验证更改：

```bash
ls -l ~/project/my_files/document.txt
```

输出应为：

```plaintext
-rwxr-xr-x 1 labex labex 0 Jun  6 17:36 /home/labex/project/my_files/document.txt
```
