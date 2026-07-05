# 使用 curl 发送 Webhook 请求

在此步骤中，你将模拟 Webhook 的投递过程。Jenkins 使用 Crumb 令牌来保护 POST 请求，因此命令首先会请求一个 Crumb 并存储匹配的 Cookie。然后，它将分支和提交作为构建参数发送到 `buildWithParameters` 接口。

将 JSON 负载中的分支和提交提取到 Shell 环境变量文件中：

```bash
python3 - <<'PY' > /tmp/webhook-trigger.env
import json
from pathlib import Path

payload = json.loads(Path("/home/labex/project/local-webhook-payload.json").read_text())
branch = payload["ref"].split("/")[-1]
commit = payload["after"]
print(f"WEBHOOK_BRANCH={branch}")
print(f"WEBHOOK_COMMIT={commit}")
PY
source /tmp/webhook-trigger.env
```

现在发送 Jenkins 构建请求：

```bash
CRUMB=$(curl -fsS -c /tmp/jenkins-webhook-build.cookies 'http://localhost:8080/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,":",//crumb)')
curl -fsS -b /tmp/jenkins-webhook-build.cookies -H "$CRUMB" \
  --data-urlencode "WEBHOOK_BRANCH=${WEBHOOK_BRANCH}" \
  --data-urlencode "WEBHOOK_COMMIT=${WEBHOOK_COMMIT}" \
  -X POST http://localhost:8080/job/webhook-trigger-demo/buildWithParameters
```

等待构建 `#1` 完成：

```bash
until curl -fsS http://localhost:8080/job/webhook-trigger-demo/1/api/json >/tmp/webhook-build-1.json 2>/dev/null && grep -q '"building":false' /tmp/webhook-build-1.json; do
  echo "Waiting for webhook build #1..."
  sleep 3
done
```

确认构建成功：

```bash
curl -fsS http://localhost:8080/job/webhook-trigger-demo/1/api/json | grep -o '"result":"SUCCESS"'
```
