# 引用 Shell 变量

在引用 Shell 变量时，有几种情况需要使用特殊的语法。让我们来探索这些案例。

1. 在 WebIDE 中打开 `variables.sh` 文件。

2. 将文件内容替换为以下内容：

   ```bash
   #!/bin/bash
   
   PRICE_PER_APPLE=5
   MyFirstLetters=ABC
   greeting='Hello        world!'
   
   # 转义特殊字符
   echo "The price of an Apple today is: \$HK $PRICE_PER_APPLE"
   
   # 避免歧义
   echo "The first 10 letters in the alphabet are: ${MyFirstLetters}DEFGHIJ"
   
   # 保留空白字符
   echo $greeting
   echo "$greeting"
   ```

3. 保存文件。

4. 运行脚本：

   ```bash
   ./variables.sh
   ```

   你应该会看到以下输出：

   ```
   The price of an Apple today is: $HK 5
   The first 10 letters in the alphabet are: ABCDEFGHIJ
   Hello world!
   Hello        world!
   ```

   注意其中的区别：

   - 第一行中对 `$` 符号进行了转义，以便将其作为普通字符打印。
   - 第二行使用花括号 `{}` 来明确界定变量名的范围。
   - 最后两行展示了在引用包含空格的变量时，使用引号与不使用引号的区别。
