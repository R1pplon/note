# 检查 Jenkins 如何存储密钥

在此步骤中，你将检查 Jenkins 写入磁盘的内容。这通常不是日常 Jenkins 工作中需要做的操作，但它能帮你理解为什么凭据比作业脚本中的明文密码更安全。

在终端中，仅打印 Jenkins `credentials.xml` 文件中的相关行：

```bash
docker exec jenkins sh -lc "grep -n -E '<id>guided-creds</id>|<username>guided-user</username>|<password>' /var/jenkins_home/credentials.xml" | tee /home/labex/project/credential-file-lines.txt
```

输出应包含凭据 ID 和用户名。密码行应包含用大括号包裹的加密 Jenkins 密钥，而不是原始文本 `guided-password-v1`。

输出示例：

```text
...<id>guided-creds</id>
...<username>guided-user</username>
...<password>{...}</password>
```

回到 Firefox 中的 Jenkins Global 域页面。凭据行也应将密码显示为掩码文本，而不是真实密码。
