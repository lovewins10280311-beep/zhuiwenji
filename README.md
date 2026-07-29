# 追文记 · 阅读工作台（静态部署仓库）

单文件阅读追踪 Web 应用（`index.html`，数据存浏览器 `localStorage`，无需后端）。
本仓库只用于托管静态站点，配合 Cloudflare Pages 部署。

## 本地更新流程

1. 直接编辑 `index.html`（或替换为新版本）。
2. 提交并推送：
   ```bash
   git add -A
   git commit -m "更新站点"
   git push origin main
   ```
3. 推送后自动上线（见下方两种部署方式任选其一）。

## 部署方式（二选一）

### 方式 A：GitHub Actions 自动部署（本仓库已配置）
适合「push 即上线」，无需进 Cloudflare 控制台。

1. 在 Cloudflare 控制台 **新建 Pages 项目时不要连 Git**（创建空项目 / Direct Upload 即可），项目名填 `zhuiwenji`。
2. 仓库 → **Settings → Secrets and variables → Actions → New repository secret**，添加两个密钥：
   - `CLOUDFLARE_API_TOKEN`：Cloudflare 控制台 → My Profile → API Tokens → 创建 Token，权限选 `Account / Cloudflare Pages: Edit`。
   - `CLOUDFLARE_ACCOUNT_ID`：Cloudflare 右侧边栏的 Account ID。
3. 推送 `main` 分支，`deploy.yml` 会自动构建并部署。可在仓库 Actions 页看日志。

> 注意：Git 集成（在 Cloudflare 里连仓库）与 GitHub Actions 是**互斥**的。用了 Actions 就不要在 Cloudflare 里连 Git，否则会冲突。

### 方式 B：Cloudflare 控制台 Git 集成（更简单，无需密钥）
1. Cloudflare → Workers & Pages → Create → Pages → **Connect to Git** → 选本仓库。
2. Framework preset：**None**；Build command：**留空**；Output directory：**`.`**。
3. Save and Deploy。`git push` 自动重新部署，无需任何 secret。

### 方式 C：Wrangler CLI 手动部署
```bash
npx wrangler login
npx wrangler pages deploy . --project-name zhuiwenji
```
本地预览：`npx wrangler pages dev . --port 8788`

## 数据迁移（重要）

`localStorage` 按「域名(origin)」隔离。换域名 / 换平台后，旧站数据**不会自动带过来**。
请在应用内「我的 → 导出 JSON」备份，再在新站点「导入 JSON」一次。

## 自定义域名

Cloudflare 项目 → Settings → Custom domains → 输入域名，按提示加 CNAME，SSL 自动签发。
