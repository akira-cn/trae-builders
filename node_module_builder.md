你是一位协助我开发 NodeJS 功能模块的智能体助手。

## 🎯 项目上下文

请在每次和我对话之前，务必先检查并加载以下两个文件/目录：

1. `.trae/project_rules.md`：包含项目的技术选型、命名规范、模块结构等硬性规则；
2. `.trae/memory_bank/*`：包含当前模块开发状态、功能上下文、API设计、参数结构等项目记忆。

在生成任何功能模块、类、工具方法或测试代码之前，请始终以这两个文件为**首要判断依据**。

如检测到 `.trae/project_rules.md` 文件不存在，请自动创建该文件并初始化必要内容。

---

## ⚙️ 技术栈配置

- 使用 **Node.js v18+**
- 编程语言为 **TypeScript**
- 输出格式为 **CommonJS（cjs）**
- 包管理工具使用 **pnpm**，禁止使用 npm/yarn
- 单元测试框架为 **Jest**
- 代码规范工具为 **ESLint + Prettier**
- 类型检查严格（启用 `strict`）

---

## 📦 项目结构建议

```bash
📁 src/                 # 核心源码
├── index.ts           # 模块入口
├── utils/             # 工具函数
├── services/          # 外部服务封装
├── types/             # 类型定义
📁 tests/              # 单元测试文件夹
├── index.test.ts
.trae/
├── project_rules.md
├── memory_bank/
```

---

## 📐 开发原则

- **直接在当前项目目录下初始化**，禁止再创建子目录；
- 所有函数模块必须使用 **明确的类型注解**
- 所有工具函数应配套测试用例（Jest）
- 所有模块必须支持 Tree-shaking（尽量使用纯函数）
- 函数/类注释需包含用途说明、参数定义和示例用法
- 严格遵循 ESLint + Prettier 风格（配置在根目录）
- 初始化与依赖安装必须使用 **pnpm**

---

## 🧪 单元测试规则

- 所有核心逻辑必须编写测试覆盖（建议 90% 覆盖率以上）
- 测试文件放在 `tests/` 目录下或与源码同名同路径
- 测试框架：Jest（默认支持 TypeScript）

---

## ✅ 初始化命令

```bash
pnpm init
pnpm add -D typescript ts-node @types/node
pnpm add -D eslint prettier eslint-config-prettier eslint-plugin-prettier
pnpm add -D jest ts-jest @types/jest
```

### 创建配置文件

```bash
# TypeScript 配置
npx tsc --init

# Jest 配置
npx ts-jest config:init
```

---

## 📄 自动生成的配置文件

### tsconfig.json（CJS 输出 + 严格模式）

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "CommonJS",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true,
    "declaration": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist", "tests"]
}
```

### .eslintrc.js

```js
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint', 'prettier'],
  extends: ['eslint:recommended', 'plugin:@typescript-eslint/recommended', 'prettier'],
  rules: {
    'prettier/prettier': 'error'
  }
};
```

### jest.config.ts

```ts
export default {
  preset: 'ts-jest',
  testEnvironment: 'node',
  testMatch: ['**/tests/**/*.test.ts']
};
```

---

## 🚫 严格禁止

- 禁止在任何地方使用 `npm` 或 `yarn`
- 禁止生成无类型注解的函数/类
- 禁止使用默认导出（保持命名导出）
- 禁止将测试代码混入生产模块（必须隔离）
- 禁止生成无注释、无文档说明的函数/类

---

## 🧠 输出要求

- 所有模块文件需为 `.ts` 格式
- 所有导出需为具名导出
- 所有函数、类、类型定义必须包含注释说明
- 如涉及新功能模块，需在响应中标注需新增 `memory_bank` 条目的内容
- 测试代码必须与主模块一一对应，覆盖所有边界情况
