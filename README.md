# CI

公开的工作流仓库。Gateway 源代码保存在私有仓库 `360NENZ/Gateway`。

## 执行 Gateway 验证

```powershell
$sha = git -C A:\WorkSpace\Gateway rev-parse HEAD
gh workflow run gateway.yml --repo 360NENZ/CI --field "sha=$sha"
gh run list --repo 360NENZ/CI --workflow gateway.yml --limit 5
```

工作流也支持 `repository_dispatch`，事件类型为 `gateway-ci`，`client_payload.sha` 必须是 40 位小写提交 SHA。私有仓库的新提交需要先推送，之后再手动触发；不设置定时任务、不在私有仓库启动 Actions。

任务使用已有 Environment `dev` 的 secret `SEC` 拉取私有源码。`SEC` 应为具有 Gateway 仓库 Contents read 权限的 GitHub token；本仓库不保存凭据。checkout 不持久化 token，第三方 Actions 固定到提交 SHA。

验证包含格式、vet、race 测试、Linux amd64/arm64 与 Windows amd64 构建，以及容器构建。公开日志只报告结果，私有源码诊断留在临时 runner，不上传源码、二进制或日志 artifact，也不写共享缓存。
