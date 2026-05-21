# 使用 chown 更改文件所有权

本步骤将教你如何使用 `chown` 命令更改文件和目录的所有者和所属组。这是一个至关重要的管理任务，因为只有 `root` 用户可以更改文件的拥有者。`labex` 用户拥有 `sudo` 权限，这将允许你执行这些操作。

`chown` 的基本语法为：`chown [OPTIONS] NEW_OWNER[:NEW_GROUP] FILE(s)`

让我们首先创建一个新用户和组，以便演示所有权更改。由于此环境是基于容器的，我们将为此演示创建简单的用户和组。

首先，创建一个名为 `devs` 的新组：

```bash
sudo groupadd devs
```

接下来，创建一个名为 `developer` 的新用户，并将其添加到 `devs` 组。我们将为此演示创建一个没有主目录或登录 shell 的系统用户。

```bash
sudo useradd -r -g devs -s /sbin/nologin developer
```

现在，让我们将 `~/project/my_files/document.txt` 的所有者从 `labex` 更改为 `developer`。

```bash
sudo chown developer ~/project/my_files/document.txt
```

使用 `ls -l` 验证更改：

```bash
ls -l ~/project/my_files/document.txt
```

输出现在应该显示 `developer` 作为所有者：

```plaintext
-rwxr--r-- 1 developer labex 0 Jun  6 00:48 /home/labex/project/my_files/document.txt
```

请注意，组所有权 (`labex`) 保持不变。

你还可以同时更改所有者和组，使用 `owner:group` 语法。让我们将 `document.txt` 的所有者更改回 `labex`，并将所属组更改为 `devs`。

```bash
sudo chown labex:devs ~/project/my_files/document.txt
```

验证更改：

```bash
ls -l ~/project/my_files/document.txt
```

输出现在应该显示 `labex` 作为所有者，`devs` 作为所属组：

```plaintext
-rwxr--r-- 1 labex devs 0 Jun  6 00:48 /home/labex/project/my_files/document.txt
```

`chown` 命令还支持 `-R` (递归) 选项，这允许你更改整个目录树的所有权。让我们将 `~/project/my_files` 目录及其所有内容的所有者更改为 `developer`，所属组更改为 `devs`。

```bash
sudo chown -R developer:devs ~/project/my_files
```

验证目录的更改：

```bash
ls -ld ~/project/my_files
```

输出应该反映新的所有权：

```plaintext
drwxr-x--- 2 developer devs 26 Jun  6 00:48 /home/labex/project/my_files
```

请注意，将目录所有权更改为 `developer:devs` 后，`labex` 用户将无法访问目录中的文件，因为目录权限为 `drwxr-x---` (所有者和组有访问权限，但其他用户没有)，而 `labex` 既不是所有者 (`developer`) 也不是组成员 (`devs`)。如果你现在尝试列出文件：

```bash
ls -l ~/project/my_files/document.txt
```

你会收到“权限被拒绝”的错误。这演示了所有权和权限如何协同工作来控制访问。

你还可以仅更改所属组，使用 `chown :NEW_GROUP FILE(s)`。这等效于使用 `chgrp` 命令。让我们将 `~/project/my_script.sh` 的所属组更改为 `devs`。

```bash
sudo chown :devs ~/project/my_script.sh
```

验证更改：

```bash
ls -l ~/project/my_script.sh
```

输出应该显示 `devs` 作为所属组所有者，而 `labex` 保持为文件所有者：

```plaintext
-rwxrwx--- 1 labex devs 41 Jun  6 00:52 /home/labex/project/my_script.sh
```

最后，让我们清理，将所有权更改回 `labex:labex`，然后删除 `developer` 用户和 `devs` 组。

```bash
sudo chown -R labex:labex ~/project/my_files
sudo userdel developer
sudo groupdel devs
```
