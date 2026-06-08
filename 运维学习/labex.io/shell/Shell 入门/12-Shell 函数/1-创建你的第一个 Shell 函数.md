# 创建你的第一个 Shell 函数

让我们从创建一个简单的 Shell 函数开始。Shell 函数就像是大型脚本中的小型脚本，允许你将执行特定任务的命令组合在一起。

首先，我们需要创建一个新文件。打开终端并输入：

```bash
cd ~/project
touch functions.sh
```

该命令会切换到 `project` 目录并创建一个名为 `functions.sh` 的新文件。这个文件将包含我们的 Shell 函数。

现在，让我们添加第一个函数：

```bash
#!/bin/bash

# 这是一个简单的函数
greet() {
  echo "Hello, World!"
}

# 这一行调用（运行）该函数
greet
```

让我们来分析一下这段代码：

- 第一行 `#!/bin/bash` 被称为 Shebang。它告诉系统使用 bash 来解释执行此脚本。
- 我们使用 `greet() { }` 来定义函数。花括号之间的所有内容都属于该函数的一部分。
- 在函数内部，我们有一个简单的 `echo` 命令，用于打印「Hello, World!」。
- 最后一行 `greet` 调用（运行）了我们的函数。

现在，让我们为脚本添加执行权限并运行它：

```bash
chmod +x functions.sh
./functions.sh
```

你应该会看到：

```
Hello, World!
```

如果你没有看到这个输出，请仔细检查你在 `functions.sh` 文件中输入的内容是否完全正确。
