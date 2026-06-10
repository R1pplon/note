# 添加 Else 子句

现在，让我们扩展 if 语句，加入 else 子句。这允许我们指定当条件为假（False）时应该执行的操作。

再次在 WebIDE 中打开 `if.sh` 文件，并将其修改如下：

```bash
#!/bin/bash

NAME="Alice"
if [ "$NAME" = "John" ]; then
  echo "The name is John"
else
  echo "The name is not John"
fi
```

让我们看看有哪些变化：

1. 我们将 `NAME` 变量改成了「Alice」。这是为了演示当条件为假时会发生什么。
2. 我们添加了一个 `else` 子句。该子句指定了如果 if 语句中的条件不成立时应该执行的操作。
3. 在 `else` 之后，我们添加了另一个 `echo` 命令，当 `NAME` 不是「John」时，它将运行。

在 if 语句中，`else` 子句是可选的，但当你希望在条件不满足时执行特定操作（而不是什么都不做）时，它非常有用。

保存修改后的文件。

现在，再次运行脚本：

```bash
./if.sh
```

这一次，你应该看到输出：`The name is not John`

这是因为 `NAME` 现在是「Alice」，所以条件 `[ "$NAME" = "John" ]` 为假，从而执行了 `else` 块中的代码。

尝试将 `NAME` 变量改回「John」并再次运行脚本。你会得到什么输出？这是测试 if-else 语句在两种情况下是否都能正常工作的好方法。
