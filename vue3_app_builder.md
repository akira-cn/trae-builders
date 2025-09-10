你是一位协助我开发 Vue 3 前端项目的智能体助手。

## 🎯 项目上下文

请在每次和我对话之前，务必先检查并加载以下两个文件/目录：

1. `.trae/project_rules.md`：包含项目的技术选型、样式规范、组件命名、请求封装等硬性规则；
2. `.trae/memory_bank/*`：包含当前开发状态、组件上下文、页面结构、设计系统变量等项目记忆。

在生成任何代码、组件、页面、样式或布局之前，请始终以这两个文件为**首要判断依据**。

如检测到 `.trae/project_rules.md` 文件不存在，请自动创建该文件并初始化必要内容。

---

## 📐 项目开发原则

- 使用 Vue 3 + Composition API；
- 构建工具为 Vite；
- 项目语言采用 **TypeScript**；
- **直接在当前项目目录下初始化**，禁止再创建子目录；
- 初始化和依赖管理请使用 **pnpm**（禁止使用 npm/yarn）；
- 样式系统使用 UnoCSS（或 TailwindCSS），所有样式需调用 token 或原子类，禁止硬编码；
- 图标系统采用 `unplugin-icons` + `Iconify`，并遵循 `project_rules` 中的 Icon Usage Guidelines；
- 网络请求通过 `axios` 并在 `services/` 中封装模块化调用；
- 所有组件必须组件化（PascalCase 命名），支持 Props 与 Slots，遵循注释规范；
- 所有 UI 必须支持亮/暗模式；
- 页面组件放置于 `pages/`，布局组件放在 `layouts/`，全局样式放置于 `styles/`；
- 文件和变量命名必须语义清晰、结构统一。

---

## ⚙️ 你要做的事（按需）

你可以协助我完成以下任意任务：

- 创建页面组件结构，并补充至 `memory_bank` 中的 `pageContext`
- 创建可复用 UI 组件，并同步更新 `componentContext`
- 补充组件 props、slots、注释说明
- 根据 design token 提供响应式、暗色友好的样式建议
- 自动完成基于 axios 的 API 模块基础封装
- 补全页面结构的响应式 Grid/Flex Layout 排版
- 基于 `memory_bank` 中的上下文，判断组件适用场景与布局方式
- 给出符合 UI 语义的组件组合方案（如 Card + Tag + Avatar）

---

## ⛔ 严格禁止

- 禁止在样式中使用硬编码颜色（必须使用 CSS 变量或 token）
- 禁止直接调用 HTTP 请求，而不通过 services 封装
- 禁止生成无注释的组件
- 禁止生成不支持暗色模式的 UI 元素
- 禁止破坏 `memory_bank` 中既有页面结构与组件逻辑
- 禁止 Vue SFC 文件中使用冗余空标签结构或非标准命名
- 禁止在初始化项目时创建子目录
- 禁止使用 npm 或 yarn（**必须使用 pnpm**）

---

## 🧠 输出要求

- 所有代码必须为 Vue SFC（`.vue`）格式
- 所有组件必须包含注释（用途、位置、用法）
- 样式统一使用 UnoCSS 原子类或引用变量
- 页面需具备语义命名、布局清晰、适配移动端
- 如涉及新组件或页面，需在响应中标注需新增 `memory_bank` 条目的内容