<div align="center">

# EXIF Gallery Nuxt

**可免费部署在 Cloudflare 的个人相册网站，支持AI图像分析，浏览器压缩图片**

[English](README.md) | [简体中文](README_zh.md)

[![Cloudflare Workers](https://img.shields.io/badge/Cloudflare-Workers-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)](https://workers.cloudflare.com/)
[![Nuxt](https://img.shields.io/badge/Nuxt-4.0-00DC82?style=for-the-badge&logo=nuxt.js&logoColor=white)](https://nuxt.com/)
[![Vue](https://img.shields.io/badge/Vue-3.5-4FC08D?style=for-the-badge&logo=vue.js&logoColor=white)](https://vuejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

</div>

---

![exif-gallery-nuxt](./public/icon.png)

## ✨ 功能特性

- 🆓 **免费 Cloudflare 部署** - 零成本托管在 Cloudflare Workers，享受丰厚的免费额度
- 🧠 **AI 智能图像分析** - 集成 OpenAI 和 Gemini，实现语义分析和智能图像描述
- 🖼️ **智能图像处理** - 浏览器端图片压缩，支持 JPEG、WebP 和 AVIF 多种格式，自动生成缩略图
- 💾 **边缘原生存储** - 基于 Cloudflare R2 对象存储和 D1 数据库，实现最优性能和全球边缘部署
- 📊 **完整 EXIF 管理** - 全面提取和展示图像元数据，包括相机设置、位置信息和时间戳
- 🏷️ **灵活的标签系统** - 通过自定义标签组织照片，支持按类别筛选
- 📑 **排序与翻页** - 排序照片，流畅的分页浏览体验
- 🎨 **现代化用户体验** - 响应式设计，流畅的视图过渡动画和精美 UI 组件
- 🔐 **安全的管理后台** - 内置身份验证系统，安全地管理照片和上传

## 🚀 快速开始

### 前置要求

- [Node.js](https://nodejs.org/) 18.x 或更高版本
- [pnpm](https://pnpm.io/)（推荐）

```bash
# 安装 pnpm（如果尚未安装）
corepack enable pnpm

# 克隆仓库
git clone https://github.com/wiidede/exif-gallery-nuxt.git
cd exif-gallery-nuxt

# 安装依赖
pnpm install

# 启动开发服务器
pnpm dev
```

访问 `http://localhost:3000` 查看应用。

## 🛠️ 技术栈

- **框架**: [Nuxt 4](https://nuxt.com/) - 直观的 Vue 框架
- **边缘平台**: [NuxtHub](https://hub.nuxt.com) - 在边缘构建全栈应用
- **数据库**: [D1](https://developers.cloudflare.com/d1/) - 边缘 SQLite 数据库
- **存储**: [R2](https://developers.cloudflare.com/r2/) - S3 兼容的对象存储
- **样式**: [UnoCSS](https://unocss.dev/) - 即时按需原子化 CSS 引擎
- **UI 组件**: [shadcn-vue](https://www.shadcn-vue.com/) + [inspira-ui](https://inspira-ui.com/)
- **状态管理**: [Pinia](https://pinia.vuejs.org/)
- **验证**: [vee-validate](https://vee-validate.logaretm.com/) + [Zod](https://zod.dev/)
- **AI**: [OpenAI](https://openai.com/) + [Google Gemini](https://gemini.google.com/)
- **代码质量**: [TypeScript](https://www.typescriptlang.org/) + [ESLint](https://eslint.org/)

## 📦 部署

### 部署到 Cloudflare Workers

本项目专为通过 NuxtHub 部署到 Cloudflare Workers 而设计。

#### 步骤 1：创建 Cloudflare 资源

1. **创建 D1 数据库**
   - 在 Cloudflare Dashboard 中导航到 **存储和数据库** → **D1 SQL 数据库**
   - 创建数据库命名为 `exif-gallery-nuxt` 并记录 **数据库 ID**

2. **创建 R2 存储桶**
   - 导航到 **存储和数据库** → **R2 对象存储**
   - 创建存储桶并记录 **存储桶名称**

#### 步骤 2：配置部署

使用您的 Cloudflare 资源 ID 更新 `wrangler.jsonc`：

```jsonc
{
  "d1_databases": [
    {
      "binding": "DB",
      "database_name": "exif-gallery-nuxt",
      "database_id": "YOUR_DATABASE_ID",
      "migrations_dir": "server/db/migrations/sqlite",
      "migrations_table": "_hub_migrations"
    }
  ],
  "r2_buckets": [
    {
      "binding": "BLOB",
      "bucket_name": "YOUR_BUCKET_NAME"
    }
  ]
}
```

#### 步骤 2.5：初始化数据库

> [!WARNING]
> **重要**：Cloudflare D1 数据库在构建时无法连接数据库，迁移**不会自动运行**。您必须手动运行迁移来创建表结构。

** 使用 GitHub Actions（推荐，自动化）**

项目已包含 `.github/workflows/migrate.yml` 文件，您可以：

1. 在 GitHub 仓库设置中添加以下 secrets：
   - `CLOUDFLARE_ACCOUNT_ID` - 您的 Cloudflare 账户 ID（在 Cloudflare Dashboard 右侧可看到）
   - `CLOUDFLARE_API_TOKEN` - 具有 D1 编辑权限的 API Token（在 Cloudflare Dashboard → My Profile → API Tokens 中创建）

2. 推送代码到 `main` 分支，或手动在 GitHub Actions 页面触发 `Database Migration` workflow

> [!NOTE]
> 本项目采用分离的迁移管理策略：
>
> - **本地开发**：NuxtHub 自动管理，在 `_hub_migrations` 表中记录
> - **云端部署**：GitHub Actions 使用 Wrangler 管理，`_hub_migrations` 表中记录，相比NuxtHub多`.sql`后缀
> - **注意**：本地开发请勿手动运行 wrangler 迁移命令，因为没有`.sql`后缀

#### 步骤 3：通过 Cloudflare Dashboard 部署

1. 前往 **Workers & Pages** → **Create application** → **Connect to Git**
2. 选择您 fork 的仓库
3. 配置构建设置：
   - **Build command**: `pnpm run build`
   - **Deploy command**: `npx wrangler deploy`
4. 添加环境变量：
   - `NUXT_SESSION_PASSWORD` - 生成一个安全的随机字符串（至少 32 位）
   - `NUXT_ADMIN_PASSWORD` - 设置您的管理后台密码
5. 点击 **Deploy**

NuxtHub 会根据 `wrangler.jsonc` 自动配置 D1 和 R2 绑定。

### 手动部署

```bash
# 构建生产版本
pnpm run build

# 部署到 Cloudflare Workers
npx wrangler deploy
```

### 远程开发

在本地连接到远程 Cloudflare 资源：

```bash
pnpm dev --remote
```

### 从 NuxtHub Admin 迁移

如果您之前使用 NuxtHub Admin 部署：

1. **更新您的 fork** 以获取最新更改

2. **获取现有资源**（从 NuxtHub 项目）：
   - D1 数据库 ID
   - R2 存储桶名称

3. **更新 `wrangler.jsonc`** 使用现有资源：

   ```jsonc
   {
     "d1_databases": [{ "binding": "DB", "database_id": "YOUR_EXISTING_DATABASE_ID" }],
     "r2_buckets": [{ "binding": "BLOB", "bucket_name": "YOUR_EXISTING_BUCKET_NAME" }]
   }
   ```

   提交并推送此更改。

4. **创建新的 Worker**（参考上方部署步骤 2-3）

5. **配置环境变量**（从旧项目复制，参考上方部署步骤 4）

6. **部署** - 数据仍保留在原本的 D1 数据库和 R2 存储桶中

## 🔧 配置

### 环境变量

| 变量                                  | 必需 | 默认值                                                         | 描述                                            |
| ------------------------------------- | ---- | -------------------------------------------------------------- | ----------------------------------------------- |
| `NUXT_ADMIN_PASSWORD`                 | 是   | `admin`                                                        | 管理后台访问密码                                |
| `NUXT_SESSION_PASSWORD`               | 是   | 自动生成                                                       | 会话加密密钥（至少32位）                        |
| `NUXT_PUBLIC_TITLE`                   | 否   | `Exif Gallery Nuxt`                                            | 应用标题                                        |
| `NUXT_PUBLIC_DESCRIPTION`             | 否   | 一个集成了 AI 智能处理、浏览器图片压缩等功能的全栈相册解决方案 | 应用描述                                        |
| `NUXT_PUBLIC_DISABLE_3D_CARD_DEFAULT` | 否   | `false`                                                        | 是否默认禁用 3D 卡片效果（设为 `true`默认禁用） |

## 📁 项目结构

```
exif-gallery-nuxt/
├── app/                    # 前端应用
│   ├── components/         # Vue 组件
│   ├── composables/       # Vue 组合式函数
│   ├── pages/             # 应用页面
│   ├── stores/            # Pinia 存储
│   ├── utils/             # 工具函数
│   └── workers/           # Web Workers
├── server/                # 后端 API
│   ├── api/               # API 路由
│   ├── db/                # 数据库模式
│   └── utils/             # 服务端工具
└── types/                 # TypeScript 类型定义
```

## 🤝 贡献

欢迎贡献代码！请随时提交 Pull Request。

1. Fork 本仓库
2. 创建您的特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交您的更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启一个 Pull Request

## 📄 许可证

本项目采用 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件。

## 🙏 致谢

- [exif-photo-blog](https://github.com/sambecker/exif-photo-blog) - EXIF 处理的灵感来源
- [nuxt-image-gallery](https://github.com/Flosciante/nuxt-image-gallery) - 相册实现参考
- [NuxtHub](https://hub.nuxt.com) - 边缘部署平台
- [shadcn-vue](https://www.shadcn-vue.com/) - 精美的 UI 组件
- [inspira-ui](https://inspira-ui.com/) - 动画 UI 组件

---

<div align="center">

由 [wiidede](https://github.com/wiidede) 用 ❤️ 制作

</div>
