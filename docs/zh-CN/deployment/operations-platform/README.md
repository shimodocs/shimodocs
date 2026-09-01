# 运维平台概览

[← ShimoDocs Suite 部署文档](../README.md)

## 功能概览

- **ShimoDocs Suite**：用于管理与授权、租户、用户、品牌以及 AI 配置相关的 ShimoDocs Suite.
- **系统服务**：用于一般操作和维护任务，如全局配置、集群管理、日志查看、功能检查、问题查询、文档修复，以及 **系统升级**.

> **注意**：显示的具体功能取决于当前部署版本和已启用的功能。

## 登录运维平台

在浏览器中访问以下地址：
> **浏览器要求**：请使用 Google Chrome 111 版本或以上访问运维平台。建议先升级到最新稳定版本。

```text
http(s)://<OPERATIONS_PLATFORM IP OR_DOMAIN_NAME>/mdp/user/login
```

输入管理员账户和 PASSWORD，然后点击“登录”。

## 了解运维平台首页

登录后，可以通过页面左侧的菜单访问相应的管理功能。显示的菜单取决于当前环境中部署和授权的产品和版本。

## 重置管理员 PASSWORD 当忘记时

如果忘记了运维平台管理员 PASSWORD ，可以登录到部署节点并执行以下命令进行重置。

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password Aa1234567.
```

上例重置了 PASSWORD 迁移到 `Aa1234567.`。在实际操作中，请将命令末尾的示例 PASSWORD 替换为符合安全要求的新 PASSWORD 。

重置完成后，返回登录页面，使用新的 PASSWORD登录，并确认菜单可以正常访问。
