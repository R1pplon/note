# 添加一个步骤以安全地输出 Secret 值

在此步骤中，你将修改工作流（workflow）以访问 Secret。GitHub Actions 允许你使用 `${{ secrets.SECRET_NAME }}` 语法来访问 Secret。

## 修改工作流

打开 `.github/workflows/secrets-demo.yml` 文件，并将以下步骤追加到 `steps` 列表中。请确保缩进正确。

```yaml
- name: Print Secret
  env:
    MY_SECRET_VAL: ${{ secrets.MY_SECRET }}
  run: |
    echo "Printing secret directly (masked): ${{ secrets.MY_SECRET }}"
    echo "Printing secret from env (masked): $MY_SECRET_VAL"
```

你完整的 `.github/workflows/secrets-demo.yml` 文件应如下所示：

```yaml
name: Secrets Demo
on: [push]

jobs:
  use-secret:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Print Secret
        env:
          MY_SECRET_VAL: ${{ secrets.MY_SECRET }}
        run: |
          echo "Printing secret directly (masked): ${{ secrets.MY_SECRET }}"
          echo "Printing secret from env (masked): $MY_SECRET_VAL"
```

## 关键概念

当你使用 Secret 时，GitHub 会自动在日志中屏蔽（mask）它。即使你尝试使用 `echo` 命令输出它，输出结果也会显示星号（`***`）而不是实际值。
