# 理解和应用特殊权限 (SUID、SGID、粘滞位)

在这一步，你将探索 Linux 中的特殊权限：SUID（Set User ID，设置用户 ID）、SGID（Set Group ID，设置组 ID）和 Sticky Bit（粘滞位）。这些权限提供了对文件执行和目录行为的增强控制。

特殊权限由八进制权限模式中的一个附加数字表示，放置在标准的三位数字（所有者、组、其他用户）之前。

- **SUID（Set User ID）：**
  - **八进制值：** 4
  - **对文件的影响：** 当运行带有 SUID 的可执行文件时，它将以文件所有者的权限执行，而不是运行它的用户的权限。这通常用于需要提升权限才能执行某些任务的程序，例如 `passwd` 命令（它需要写入 `/etc/shadow`，一个由 `root` 拥有的文件）。
  - **在 `ls -l` 输出中：** `s` 出现在所有者的 `x`（执行）权限的位置。如果所有者没有执行权限，则会出现大写的 `S`。
- **SGID（Set Group ID）：**
  - **八进制值：** 2
  - **对文件的影响：** 类似于 SUID，但可执行文件以文件组所有者的权限运行。
  - **对目录的影响：** 在启用 SGID 的目录中创建的文件和子目录将继承该目录的组所有权，而不是创建它们的用户的首要组。这对于所有文件都应属于特定组的共享目录非常有用。
  - **在 `ls -l` 输出中：** `s` 出现在组的 `x`（执行）权限的位置。如果组没有执行权限，则会出现大写的 `S`。
- **Sticky Bit（粘滞位）：**
  - **八进制值：** 1
  - **对文件的影响：** 无影响。
  - **对目录的影响：** 用户可以在目录中创建文件，但他们只能删除或重命名他们拥有的文件。这可以防止用户在共享目录（例如 `/tmp`）中删除或移动其他用户的文件。
  - **在 `ls -l` 输出中：** `t` 出现在其他用户的 `x`（执行）权限的位置。如果其他用户没有执行权限，则会出现大写的 `T`。

让我们演示这些特殊权限。

### SUID 示例

我们将创建一个简单的 C 程序，尝试读取一个受限文件。

首先，创建一个只有 `root` 才能读取的文件：

```bash
sudo touch ~/project/secret_data.txt
sudo chmod 600 ~/project/secret_data.txt
sudo chown root:root ~/project/secret_data.txt
```

验证其权限：

```bash
ls -l ~/project/secret_data.txt
```

输出：

```plaintext
-rw------- 1 root root 0 Jun  6 17:36 /home/labex/project/secret_data.txt
```

现在，创建一个 C 程序 `read_secret.c`，尝试读取此文件：

```bash
nano ~/project/read_secret.c
```

将以下代码粘贴到 `read_secret.c` 中：

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    FILE *fp;
    char buffer[256];

    printf("Attempting to read /home/labex/project/secret_data.txt...\n");

    fp = fopen("/home/labex/project/secret_data.txt", "r");
    if (fp == NULL) {
        perror("Error opening file");
        return 1;
    }

    while (fgets(buffer, sizeof(buffer), fp) != NULL) {
        printf("%s", buffer);
    }

    fclose(fp);
    printf("Successfully read file.\n");
    return 0;
}
```

保存并退出 `nano`（Ctrl+S，Ctrl+X）。

编译程序：

```bash
gcc ~/project/read_secret.c -o ~/project/read_secret
```

现在，尝试以 `labex` 的身份运行它：

```bash
~/project/read_secret
```

你应该看到一条“Error opening file: Permission denied”（打开文件错误：权限被拒绝）消息，因为 `labex` 没有读取 `secret_data.txt` 的权限。

现在，让我们让 `read_secret` 归 `root` 所有并设置 SUID 位。

```bash
sudo chown root:root ~/project/read_secret
sudo chmod u+s ~/project/read_secret
```

验证权限：

```bash
ls -l ~/project/read_secret
```

输出：

```plaintext
-rwsr-xr-x 1 root root 17704 Jun  6 01:02 /home/labex/project/read_secret
```

注意所有者权限集中的 `s`。现在，再次以 `labex` 的身份运行该程序：

```bash
~/project/read_secret
```

这次，它应该成功读取文件（尽管它是空的，因此不会打印任何内容，但“Successfully read file.”消息表示成功）。这是因为 SUID 位使程序以 `root` 的权限运行。

### SGID 示例（在目录上）

让我们创建一个共享目录和一个新组。

```bash
sudo groupadd shared_group
sudo mkdir ~/project/shared_dir
sudo chown labex:shared_group ~/project/shared_dir
sudo chmod 770 ~/project/shared_dir
```

现在，在 `shared_dir` 上设置 SGID 位：

```bash
sudo chmod g+s ~/project/shared_dir
```

验证权限：

```bash
ls -ld ~/project/shared_dir
```

输出：

```plaintext
drwxrws--- 2 labex shared_group 6 Jun  6 01:02 /home/labex/project/shared_dir
```

注意组的权限集中的 `s`。

现在，在 `shared_dir` 内部创建一个文件：

```bash
touch ~/project/shared_dir/new_file.txt
```

检查 `new_file.txt` 的所有权：

```bash
ls -l ~/project/shared_dir/new_file.txt
```

输出：

```plaintext
-rw-r--r-- 1 labex shared_group 0 Jun  6 01:02 /home/labex/project/shared_dir/new_file.txt
```

即使 `labex` 的首要组是 `labex`，由于 SGID 位，`new_file.txt` 继承了 `shared_group` 组所有权。

### Sticky Bit 示例

`/tmp` 目录是设置了粘滞位的目录的经典示例。让我们创建一个类似的目录。

```bash
sudo mkdir ~/project/public_upload
sudo chmod 1777 ~/project/public_upload
```

`1777` 中的 `1` 是粘滞位的八进制值。`777` 授予所有者、组和其他用户完全的权限。

验证权限：

```bash
ls -ld ~/project/public_upload
```

输出：

```plaintext
drwxrwxrwt 2 root root 6 Jun  6 01:02 /home/labex/project/public_upload
```

注意其他用户的权限集中的 `t`。

现在，让我们模拟另一个用户在此目录中创建文件。由于我们只有 `labex` 用户，我们将以 `labex` 的身份创建一个文件，然后在将其所有权更改为 `root` 后尝试将其删除（模拟另一个用户）。

以 `labex` 的身份创建一个文件：

```bash
touch ~/project/public_upload/labex_file.txt
```

将其所有权更改为 `root`：

```bash
sudo chown root:root ~/project/public_upload/labex_file.txt
```

现在，尝试以 `labex` 的身份删除 `labex_file.txt`：

```bash
rm ~/project/public_upload/labex_file.txt
```

你将看到一个提示，询问你是否要删除受写保护的文件，在用 `y` 确认后，你将收到一个“Operation not permitted”（不允许操作）错误。这是因为粘滞位阻止用户删除他们不拥有的该目录中的文件，即使 `labex` 具有对 `public_upload` 目录的写权限。只有 `root` 或 `labex_file.txt` 的所有者（在本例中为 `root`）才能将其删除。

要清理，你需要 `sudo` 来删除 `labex_file.txt`：

```bash
sudo rm ~/project/public_upload/labex_file.txt
```

### 清理

删除创建的文件和目录，以及用户/组：

```bash
sudo rm -f ~/project/secret_data.txt ~/project/read_secret.c ~/project/read_secret
sudo rm -rf ~/project/shared_dir ~/project/public_upload
sudo groupdel shared_group
```
