# 高级函数 - ENGLISH_CALC

现在我们已经涵盖了 Shell 函数的基础知识，让我们创建一个名为 `ENGLISH_CALC` 的更高级的函数。该函数将接受三个参数：两个数字和一个操作符（plus、minus 或 times）。

将内容替换为以下代码：

```bash
#!/bin/bash

ENGLISH_CALC() {
  local num1=$1
  local operation=$2
  local num2=$3
  local result

  case $operation in
    plus)
      result=$((num1 + num2))
      echo "$num1 + $num2 = $result"
      ;;
    minus)
      result=$((num1 - num2))
      echo "$num1 - $num2 = $result"
      ;;
    times)
      result=$((num1 * num2))
      echo "$num1 * $num2 = $result"
      ;;
    *)
      echo "Invalid operation. Please use 'plus', 'minus', or 'times'."
      return 1
      ;;
  esac
}

# 测试函数
ENGLISH_CALC 3 plus 5
ENGLISH_CALC 5 minus 1
ENGLISH_CALC 4 times 6
ENGLISH_CALC 2 divide 2 # 这应该会显示一条错误消息
```

让我们分析一下这个函数：

- 我们使用 `local` 变量来存储输入和结果。这是避免干扰任何全局变量的良好实践。
- 我们使用 `case` 语句来处理不同的操作。这类似于其他语言中的 switch 语句。
- 对于每个有效的操作，我们执行计算并输出结果。
- 如果提供了无效的操作，我们会输出一条错误消息并返回 1（在 Shell 脚本中，非零返回值表示错误）。
- 最后，我们使用各种输入测试函数，包括一个无效的操作。

保存文件并运行：

```bash
./functions.sh
```

你应该会看到以下输出：

```
3 + 5 = 8
5 - 1 = 4
4 * 6 = 24
Invalid operation. Please use 'plus', 'minus', or 'times'.
```

如果你没有看到这个输出，请检查 `functions.sh` 文件中是否有拼写错误。
