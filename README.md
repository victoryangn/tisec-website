# TISEC Website — 泰世科技官网

> **TISEC TECH LIMITED** 企业官网
> 🌐 [tisech.com](https://tisech.com) | 🏭 军品出口代理、防弹装备、战术服装

---

## 📋 项目概览

Hugo 静态网站，双语言（中文/英文），部署在 GitHub Pages，面向国际军警采购客户展示公司产品。

- **公司**: 泰世科技有限公司 (TISEC TECH LIMITED)
- **域名**: tisech.com（HTTPS 强制）
- **中文站**: `/`（根路径）| **英文站**: `/en/`
- **产品数量**: 109 个（中英文各 109 个 Markdown 文件）
- **产品图片**: 372 张（JPG/PNG）

---

## 🛠 技术栈

| 组件 | 版本/说明 |
|------|----------|
| **Hugo** | v0.160.1 Extended（必须用 Extended 版） |
| **主题** | 自定义主题 `themes/hexing/`（非子模块） |
| **CMS** | DecapCMS（`/admin/` 后台可视化编辑） |
| **部署** | GitHub Actions → GitHub Pages |
| **CSS** | 单文件 `themes/hexing/assets/css/main.css` |

---

## 🔄 部署流程

```
git push origin main
    → GitHub Actions (.github/workflows/hugo-deploy.yml)
    → Hugo build --minify
    → GitHub Pages deploy
    → tisech.com 更新（通常1-2分钟）
```

**关键配置**：
- `hugo.toml` 的 `baseURL = "https://tisech.com/"`
- `static/CNAME` 文件内容为 `tisech.com`
- ⚠️ **不要**在 Actions 里加 `--baseURL` 参数，否则会覆盖自定义域名

---

## 🏢 公司信息

- **公司全称**: 泰世科技有限公司
- **英文**: TISEC TECH LIMITED
- **地址**: Room B, 15F, Kin Hing Industrial Building, 17-23 Shek Kin Street, Kwai Chung, NT, Hong Kong
- **联系人**: Victor Yang +86 13402060841
- **主营**: 军品出口代理、防弹装备（衣/盔/板/盾）、军警制服、战术服装、冲锋衣、帐篷背包
- **核心市场**: 菲律宾、马来西亚、索马里、加纳等

---

## ⚠️ 已知陷阱 & 注意事项

### 1. GitHub Pages + 自定义域名
`hugo-deploy.yml` 中 `configure-pages` action 输出的 `base_url` 是 `victoryangn.github.io/tisec-website`，**不要用它**作为 Hugo 的 `--baseURL`，否则所有链接会带 `/tisec-website/` 前缀。让 Hugo 用 `hugo.toml` 中的 `baseURL` 即可。

### 2. 产品卡片白底
产品图片如果背景浅色+`object-fit: cover`，会看不清。确保 `.product-card` 和 `.product-card img` 的背景色设为 `#fff`，并用 `object-fit: contain` 让图片完整显示。

### 3. Hugo Extended 必须
本站使用了 SCSS/asset pipeline 功能，**必须**用 Hugo Extended 版本，普通版会报错。

### 4. 双语言内容同步
中英文产品必须**一一对应**。添加产品时记得同时创建 `content/zh/` 和 `content/en/` 下的文件。

### 5. 图片路径
frontmatter 中的 `images` 路径以 `/images/` 开头（绝对路径），对应 `static/images/` 目录。

---

## 🤖 AI Agent 协作指南

本仓库由 **Wmac**（Hermes Agent）维护，也支持其他 AI 编程工具（Claude Code、Codex 等）协作。

### 给 Agent 的建议

1. **修改模板** → 改 `themes/hexing/layouts/` 下的 `.html` 文件
2. **修改样式** → 改 `themes/hexing/assets/css/main.css`（单文件CSS）
3. **修改配置** → 改 `hugo.toml`（菜单、语言、参数）
4. **添加产品** → 在 `content/{zh,en}/products/<slug>/` 添加 `.md`
5. **产品数据源** → 中国CCGK (chinaccgk.cn) 是主要产品数据来源
6. **部署** → push 到 main 分支即可，GitHub Actions 自动构建部署
7. **验证** → 本地 `hugo server -D` 预览，或 push 后访问 tisech.com
