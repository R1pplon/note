# 在不更改 ID 的情况下更新凭据

在此步骤中，你将通过 Jenkins 控制面板更新凭据描述，同时保持凭据 ID 不变。这展示了初学者首先需要掌握的核心理念：作业应引用稳定的 ID（如 `guided-creds`），而管理员可以在后续调整凭据元数据。

在 `Global` 凭据页面上：

点击 `guided-creds` 凭据，然后点击 `Update credential`。保持 `Username`、`Password` 和 `ID` 不变。将 `Description` 修改为 `Updated guided lab credential`，然后点击 `Save`。

保存后，返回 `Global` 域页面。相同的凭据 ID 应该仍然可见，但描述现在应变为 `Updated guided lab credential`。

![Jenkins 全局域中已更新的凭据](https://file.labex.io/namespace/33fa8aba-d546-42e9-9692-64968aeaf0cc/jenkins/lab-storing-credentials-in-jenkins/zh/../assets/step4-credential-details.png)

运行以下命令，确认 Jenkins 仍保留相同的 ID 和更新后的描述：

```bash
docker exec jenkins sh -lc "grep -n -E '<id>guided-creds</id>|<description>Updated guided lab credential</description>|<password>' /var/jenkins_home/credentials.xml" | tee /home/labex/project/updated-credential-lines.txt
```

密码应保持加密状态，且不应出现原始文本 `guided-password-v1`。
