# for 循环

`for` 循环用于遍历一系列值。这就像在说：「对于列表中的每个项目，执行某些操作。」让我们创建一个脚本来演示如何使用 `for` 循环。

在 `bash_loops` 目录中创建一个名为 `for_loop.sh` 的新文件：

```bash
touch for_loop.sh
```

现在，在 WebIDE 中打开 `for_loop.sh` 文件并添加以下内容：

```bash
#!/bin/bash

# 遍历名称数组
echo "Looping through an array:"
NAMES=("Alice" "Bob" "Charlie" "David")
for name in "${NAMES[@]}"; do
  echo "Hello, $name!"
done

echo # 打印空行以提高可读性

# 遍历数字范围
echo "Looping through a range of numbers:"
for i in {1..5}; do
  echo "Number: $i"
done
```

让我们分解一下这个脚本的作用：

1. 第一个循环遍历一个名称数组。对于数组中的每个名称，它都会打印一条问候语。
2. 第二个循环使用范围 `{1..5}` 从 1 数到 5。

`"${NAMES[@]}"` 这种语法可能看起来很奇怪。`@` 表示「数组的所有元素」，而引号和大括号确保每个元素都被视为一个独立的项，即使它包含空格也是如此。

保存文件并使用此命令使其可执行：

```bash
chmod +x for_loop.sh
```

`chmod +x` 命令使文件具有可执行权限，这意味着你可以将其作为程序运行。

现在，运行脚本：

```bash
./for_loop.sh
```

你应该会看到如下输出：

```
Looping through an array:
Hello, Alice!
Hello, Bob!
Hello, Charlie!
Hello, David!

Looping through a range of numbers:
Number: 1
Number: 2
Number: 3
Number: 4
Number: 5
```

这演示了 `for` 循环如何遍历数组和数字范围。
