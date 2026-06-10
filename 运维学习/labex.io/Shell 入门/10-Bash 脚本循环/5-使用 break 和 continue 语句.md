# 使用 break 和 continue 语句

`break` 和 `continue` 语句用于控制循环的流程。`break` 会提前退出循环，而 `continue` 会跳过当前迭代的剩余部分并进入下一次迭代。

创建一个名为 `break_continue.sh` 的新文件：

```bash
touch break_continue.sh
```

在 WebIDE 中打开 `break_continue.sh` 文件并添加以下内容：

```bash
#!/bin/bash

# 使用 break 提前退出循环
echo "Demonstration of break:"
for i in {1..10}; do
  if [ $i -eq 6 ]; then
    echo "Breaking the loop at $i"
    break
  fi
  echo $i
done

echo # 打印空行以提高可读性

# 使用 continue 跳过迭代
echo "Demonstration of continue (printing odd numbers):"
for i in {1..10}; do
  if [ $((i % 2)) -eq 0 ]; then
    continue
  fi
  echo $i
done
```

让我们分解这个脚本：

1. 在第一个循环中，当 `i` 等于 6 时，我们使用 `break` 退出循环。
2. 在第二个循环中，我们使用 `continue` 跳过偶数。条件 `$((i % 2)) -eq 0` 用于检查一个数是否为偶数（即除以 2 余数为 0）。

`%` 运算符用于计算除法后的余数。因此，对于偶数，`i % 2` 的结果为 0，对于奇数，结果为 1。

保存文件并使其可执行：

```bash
chmod +x break_continue.sh
```

现在，运行脚本：

```bash
./break_continue.sh
```

你应该会看到如下输出：

```
Demonstration of break:
1
2
3
4
5
Breaking the loop at 6

Demonstration of continue (printing odd numbers):
1
3
5
7
9
```

这演示了 `break` 如何提前退出循环，以及 `continue` 如何根据条件跳过某些迭代。
