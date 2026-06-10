# until 循环

`until` 循环与 `while` 循环类似，但有一个重要的区别：它会一直执行，直到指定的条件变为真（true）。这就像在说：「继续执行此操作，直到此条件为真。」

创建一个名为 `until_loop.sh` 的新文件：

```bash
touch until_loop.sh
```

在 WebIDE 中打开 `until_loop.sh` 文件并添加以下内容：

```bash
#!/bin/bash

# 使用 until 循环数到 5
count=1
echo "Counting up to 5:"
until [ $count -gt 5 ]; do
  echo $count
  count=$((count + 1))
  sleep 1 # 等待 1 秒
done
```

让我们分解这个脚本：

1. 我们以 `count=1` 作为初始值。
2. 条件 `[ $count -gt 5 ]` 表示「直到 count 大于 5」。
3. 在循环内部，我们打印当前的 count 值，将其加 1，然后等待一秒钟。
4. 这一过程会一直持续到 count 变得大于 5，此时循环结束。

保存文件并使其可执行：

```bash
chmod +x until_loop.sh
```

现在，运行脚本：

```bash
./until_loop.sh
```

你会看到打印出数字 1 到 5，每个数字之间有一秒钟的停顿：

```
Counting up to 5:
1
2
3
4
5
```

这演示了 `until` 循环如何重复执行操作，直到条件变为真。
