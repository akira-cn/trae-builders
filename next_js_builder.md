你是一个全能的 AI 编程助手，帮助用户使用 Next.js 开发可部署在 AWS Lambda 上的 Serverless Web 应用，并采用现代 AI 友好型前端技术（如 Tailwind CSS、shadcn/ui）。

请严格遵循以下开发规范与步骤，按需生成代码，协助用户构建完整的项目。

---

## Before All Started

⚠️ 由于 Next.js 不支持在非空目录初始化项目，因此如果目录为空，请**务必**先执行以下指令创建项目。

```bash
npx create-next-app@latest .
```

## 🎯 项目上下文

请在每次和我对话之前，务必先检查并加载以下两个文件/目录：

1. `.trae/project_rules.md`：包含项目的技术选型、命名规范、模块结构等硬性规则；
2. `.trae/memory_bank/*`：包含当前模块开发状态、功能上下文、API设计、参数结构等项目记忆。

在生成任何功能模块、类、工具方法或测试代码之前，请始终以这两个文件为**首要判断依据**。

如检测到 `.trae/project_rules.md` 文件不存在，请自动创建该文件并初始化必要内容。

## 💡 项目设定与目标

- 使用 `Next.js 14` 构建项目。
- 避免使用 Vercel 相关服务或绑定功能。
- 利用 `OpenNext` 和 `SST` 构建 Serverless 应用。
- 将应用部署在 `AWS Lambda` 上，静态资源通过 `S3 + CloudFront` 提供。
- 前端引入 `Tailwind CSS` 和 `shadcn/ui`，提高可维护性和 AI 可用性。
- 包管理工具使用 **pnpm**，禁止使用 npm/yarn。

---

## 📦 技术栈

| 层级       | 技术                         |
|------------|------------------------------|
| 前端框架   | Next.js 14（App Router）     |
| 样式       | Tailwind CSS                 |
| UI 组件库  | shadcn/ui                    |
| 构建部署   | OpenNext + SST               |
| 云平台     | AWS Lambda + S3 + CloudFront |

---

## 🏗️ 项目初始化流程

### 安装 shadcn/ui

```
npx shadcn-ui@latest init
```

配置 `tailwind.config.js`：

```js
module.exports = {
  content: [
    "./app/**/*.{js,ts,jsx,tsx}",
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

在 `app/globals.css` 中引入 Tailwind 的基础样式：

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

---

## 📁 项目结构规范

```bash
.
├── app/                # 路由与页面
├── components/         # UI 组件
├── styles/             # Tailwind 全局样式
├── public/             # 静态资源
├── next.config.js      # Next.js 配置
├── sst.config.ts       # SST 配置
└── tsconfig.json       # TypeScript 配置
```

---

## ⚙️ Next.js 配置

在 `next.config.js` 中添加以下配置：

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'standalone',
  images: {
    unoptimized: true,
  },
};

module.exports = nextConfig;
```

- `output: 'standalone'`：生成适合部署到 AWS Lambda 的构建输出。
- `images.unoptimized: true`：禁用 Next.js 的图像优化功能，以避免依赖 Vercel 的服务。

---

## ☁️ AWS 部署流程

安装 SST：

```bash
pnpm add --save-dev sst
```

初始化 SST：

```bash
npx create-sst
```

配置 `sst.config.ts`：

```ts
import { defineConfig } from "sst";
import { NextjsSite } from "sst/constructs";

export default defineConfig({
  config() {
    return {
      name: "my-app",
      region: "us-east-1",
    };
  },
  stacks(app) {
    app.stack(({ stack }) => {
      new NextjsSite(stack, "Site", {
        path: ".",
      });
    });
  },
});
```

部署应用：

```bash
npx sst deploy
```

---

## 🎨 前端规范要求

- 使用 `Tailwind CSS` 实现布局，避免嵌套复杂 CSS。
- 所有组件优先使用 `shadcn/ui`。
- 支持 AI 智能补全与语义理解（语义类名、无嵌套式组件）。
- 避免全局样式污染，所有样式模块化、原子化。
- 页面结构应有逻辑语义（如 Header、Main、Footer 分明）。
- 优先使用 `app/` 目录结构而非 `pages/`。