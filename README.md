# M365-Copilot2API-docker

[M365-Copilot2API](https://github.com/HEXUXIU/M365-Copilot2API) 的 Docker 镜像分发仓库：把 M365 Copilot 的 ChatHub 私有协议转换为 OpenAI / Anthropic 兼容 API 的 Go 网关，由 GitHub Actions 自动构建 **linux/amd64 + linux/arm64** 双架构镜像并发布到 GHCR。

> 上游项目遵循 AGPL-3.0 + 附加条款（禁止作为付费 API 中转服务运营），本仓库原样保留其 [LICENSE](LICENSE)。

## 快速开始

```bash
mkdir m365 && cd m365
# 下载本仓库的 docker-compose.yml 到当前目录
curl -O https://raw.githubusercontent.com/ruoshui6662/M365-Copilot2API-docker/main/docker-compose.yml
docker compose up -d
```

浏览器打开 `http://127.0.0.1:4141`，用默认密码 `admin123` 登录并按提示修改，然后在控制台添加你的 Microsoft 账号授权即可调用：

```bash
curl http://127.0.0.1:4141/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"gpt-4o","messages":[{"role":"user","content":"hello"}]}'
```

## 镜像标签

| 标签 | 说明 |
|------|------|
| `latest` | 最新发布版本 |
| `vX.Y.Z` | 与 Release tag 对应的固定版本 |
| `<commit-sha>` | 每次构建的精确版本 |

```bash
docker pull ghcr.io/ruoshui6662/m365-copilot2api-docker:latest
```

## 配置

所有配置通过环境变量完成，默认值已烘焙进镜像，最小配置即可运行。常用项：

| 变量 | 默认值 | 说明 |
|------|--------|------|
| `M365_ADMIN_PASSWORD` | `admin123`（首次登录强制修改） | 控制台与管理 API 的管理员密码 |
| `M365_LISTEN` | `0.0.0.0:4141` | HTTP 监听地址 |
| `M365_OUTBOUND_PROXY` | 空 | 出站代理，如 `http://host.docker.internal:7890` |
| `M365_PUBLIC_IDENTITY_POLICY` | `false` | 公开身份输出中性化策略 |

完整变量列表见 [.env.example](.env.example) 与[上游 README](https://github.com/HEXUXIU/M365-Copilot2API#配置说明)。

## 数据持久化

账号凭据、Token 缓存、会话等全部写在容器内 `/data`，compose 已映射到宿主机 `./data` 目录，升级镜像不丢失。`data/` 含敏感凭据，已在 `.gitignore` 中排除，请勿提交或外传。

## 从源码构建

```bash
docker build -t m365-copilot2api:local .
```

或使用 `docker compose up -d --build`（将 compose 中 `image:` 换成本地标签并添加 `build: .`）。

## 与上游的关系

- 源码完整同步自 [HEXUXIU/M365-Copilot2API](https://github.com/HEXUXIU/M365-Copilot2API)，本仓库仅新增 Docker 发布层：`.github/workflows/docker.yml`、面向最终用户的 `docker-compose.yml`
- 协议转换、会话管理等功能问题请移步上游 Issues；镜像构建问题请在本仓库提 Issue
