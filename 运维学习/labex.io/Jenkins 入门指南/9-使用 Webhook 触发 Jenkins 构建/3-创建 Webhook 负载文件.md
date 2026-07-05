# 创建 Webhook 负载文件

在此步骤中，你将创建一个 JSON 文件，其格式类似于小型源代码控制 Webhook 的负载（Payload）。真实的 Webhook 负载包含许多字段，本实验仅保留构建所需的字段：用于分支的 `ref` 和用于提交 ID 的 `after`。

加载已保存的分支和提交值，然后写入 JSON 负载：

```bash
cd /home/labex/project
source webhook-values.env
cat > local-webhook-payload.json <<JSON
{
  "ref": "refs/heads/${WEBHOOK_BRANCH}",
  "after": "${WEBHOOK_COMMIT}",
  "repository": {
    "full_name": "labex/webhook-demo"
  }
}
JSON
```

使用 Python 的 JSON 格式化工具确认文件有效且可读：

```bash
python3 -m json.tool /home/labex/project/local-webhook-payload.json
```

你应该能看到 `refs/heads/main` 分支引用以及来自 Git 仓库的提交 ID。
