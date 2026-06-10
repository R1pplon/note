# 引入 Elif

有时，我们需要检查多个条件。这时 `elif`（即 else if）子句就派上用场了。让我们修改脚本以处理多个名字。

将 `if.sh` 文件更新为以下内容：

```bash
#!/bin/bash

NAME="George"
if [ "$NAME" = "John" ]; then
  echo "John Lennon"
elif [ "$NAME" = "Paul" ]; then
  echo "Paul McCartney"
elif [ "$NAME" = "George" ]; then
  echo "George Harrison"
elif [ "$NAME" = "Ringo" ]; then
  echo "Ringo Starr"
else
  echo "Unknown member"
fi
```

让我们分析一下这个脚本：

1. 我们以 `NAME="George"` 开始。这是我们要检查的名字。
2. 第一个 `if` 语句检查名字是否为「John」。
3. 如果不是「John」，则进入第一个 `elif`（else if）语句，检查名字是否为「Paul」。
4. 如果不是「Paul」，则进入下一个 `elif`，检查是否为「George」。
5. 如果不是「George」，则检查是否为「Ringo」。
6. 如果以上条件都不满足，则进入 `else` 子句，输出「Unknown member」。

`elif` 子句允许你按顺序检查多个条件。你可以根据需要添加任意数量的 `elif` 子句。条件会按顺序被检查，第一个为真的条件将执行其对应的代码块。

保存修改后的文件。

现在，运行脚本：

```bash
./if.sh
```

你应该看到输出：`George Harrison`

尝试将 `NAME` 变量更改为不同的值（如「John」、「Paul」、「Ringo」或其他完全不同的内容）并每次运行脚本。观察输出如何根据 `NAME` 的值而变化。
