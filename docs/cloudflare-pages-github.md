# Cloudflare Pages + GitHub 部署清单

本仓库是 `cmliu/edgetunnel` 的 GitHub fork，用于通过 Cloudflare Pages 连接 Git 部署。

## GitHub 仓库

- 仓库: `https://github.com/JinRudy/edgetunnel-cf-pages`
- 上游: `https://github.com/cmliu/edgetunnel`
- 默认分支: `main`

## Cloudflare Pages 设置

1. 打开 Cloudflare Dashboard，进入 `Workers & Pages` > `Pages`。
2. 选择 `Create application` > `Pages` > `Connect to Git`。
3. 授权 GitHub 后选择仓库 `JinRudy/edgetunnel-cf-pages`。
4. 构建设置保持静态/无构建：
   - Framework preset: `None`
   - Build command: 留空
   - Build output directory: `/`
5. 在 `Environment variables (advanced)` 添加生产环境变量：
   - `ADMIN`: 后台登录密码，使用强随机值
   - `KEY`: 订阅路径密钥，使用强随机值
   - `UUID`: UUIDv4，用于固定节点 UUID
   - `OFF_LOG`: `true`
6. 保存并部署。

## KV 绑定

首次部署后进入 Pages 项目设置：

1. 打开 `Settings` > `Functions` > `KV namespace bindings`。
2. 新建或选择一个 KV namespace。
3. 绑定变量名必须填写 `KV`。
4. 保存后重新部署一次。

## 自定义域名

建议绑定 Cloudflare 托管域名下的二级域名，例如 `edge.example.com`：

1. 在 Pages 项目打开 `Custom domains`。
2. 添加二级域名，不要使用根域名。
3. 按 Cloudflare 提示完成 CNAME 或自动 DNS 记录。
4. 等待证书状态变为 Active。

## Clash Verge 使用

部署完成后：

1. 打开 `https://<your-domain>/admin`，输入 `ADMIN` 登录。
2. 在后台生成 Clash / Mihomo 订阅。
3. 在 Clash Verge Rev 中添加订阅 URL。
4. 优先使用规则模式，只代理需要的域名流量。

订阅快捷入口通常是：

```text
https://<your-domain>/<KEY>
```

## 本地生成密钥

不要把以下值提交到 GitHub。只在 Cloudflare 环境变量里填写。

```bash
openssl rand -base64 24
uuidgen | tr '[:upper:]' '[:lower:]'
```

## 同步上游更新

```bash
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

## 注意

- Cloudflare 免费账号的 Workers/Pages Functions 有请求、CPU、连接等限制，不建议全局代理大流量。
- 不建议把 `ADMIN`、`KEY`、`UUID`、上游代理账号等敏感信息写入仓库文件。
- 使用前确认当地法律法规和 Cloudflare 服务条款。
