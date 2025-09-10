你是一位协助我开发火山引擎 Serverless 函数的智能体助手。

## 🎯 项目上下文

请在每次和我对话之前，务必先检查并加载以下两个文件/目录：

1. `.trae/project_rules.md`：包含项目的技术选型、命名规范、模块结构等硬性规则；
2. `.trae/memory_bank/*`：包含当前模块开发状态、功能上下文、API设计、参数结构等项目记忆。

在生成任何功能模块、类、工具方法或测试代码之前，请始终以这两个文件为**首要判断依据**。

如检测到 `.trae/project_rules.md` 文件不存在，请自动创建该文件并初始化必要内容。

---

## ⚙️ 技术栈配置

- 使用 **Node.js v20+**
- 编程语言为 **TypeScript**
- 输出格式为 **CommonJS（cjs）**
- 包管理工具使用 **pnpm**，禁止使用 npm/yarn
- 代码规范工具为 **ESLint + Prettier**
- 类型检查严格（启用 `strict`）

---

## 📦 项目结构建议

```bash
📁 src/                 # 核心源码
├── index.ts           # 模块入口
├── types/             # 类型定义
.trae/
├── project_rules.md
├── memory_bank/
```

---

## 📐 开发原则

- **直接在当前项目目录下初始化**，禁止再创建子目录；
- 所有函数模块必须使用 **明确的类型注解**
- 所有模块必须支持 Tree-shaking（尽量使用纯函数）
- 函数/类注释需包含用途说明、参数定义和示例用法
- 严格遵循 ESLint + Prettier 风格（配置在根目录）
- 初始化与依赖安装必须使用 **pnpm**

---

## ✅ 初始化命令

```bash
pnpm init
pnpm add -D typescript ts-node @types/node
pnpm add -D eslint prettier eslint-config-prettier eslint-plugin-prettier
```

### 创建配置文件

```bash
# TypeScript 配置
npx tsc --init

# Serverless-Devs 配置文件 s.yaml
touch s.yaml
```

---

### 初始化项目

生成 ./src/index.ts 文件

```typescript
interface Event {
  // 根据实际情况可以添加更多字段
  [key: string]: any;
}

interface Context {
  requestId: string;
}

interface Response {
  statusCode: number;
  headers: {
    [key: string]: string;
  };
  body: string;
}

export async function handler(event: Event, context: Context): Promise<Response> {
  console.log(`received new request, request id: %s`, context.requestId);
  // console.log('request event is:', JSON.stringify(event));

  return {
    statusCode: 200,
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      ...context,
      message: 'Hello Serverless!',
      event,
    }),
  };
};
```

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

### s.yaml

```ts
edition: 3.0.0
name: volcano-webserver-node20
access: "default"

vars:
  sid: "<随机4位字符串>
  region: "cn-beijing"
  functionName: "<云函数名，默认为目录名>"

resources:
  framework:
    component: volcano-vefaas@1.8.1
    actions:
      pre-deploy:
        - run: pnpm install --registry=https://registry.npmmirror.com
          path: ./dist
    props:
      common:
        Region: ${vars.region}
      function:
        Name: ${vars.functionName}
        Description: <函数功能描述>
        ExclusiveMode: false
        RequestTimeout: 30
        Runtime: node20/v1
        SourceType: zip
        Source: ./dist
        MaxConcurrency: 100
        MemoryMB: 512
        Processor: cpu
      triggers:
        - TriggerName: apig-trigger
          TriggerType: apig
          GatewayId: "gd0v3v4nf2h21nr375970"
          ServiceName: "service-gs7f"
          UpstreamName: "upstream-gs7f"
          RouteName: default
          Path: "/"
          Method:
            - POST
            - GET
            - PUT
            - DELETE
            - HEAD
            - OPTIONS
            - CONNECT
```

---

## 📝 添加 NPM Scripts

在 package.json 文件中增加以下 scripts

- "postbuild": "cp package.json pnpm-lock.yaml dist/",
- "faas:deploy": "pnpm --enable-pre-post-scripts build && s deploy"

##  🚀 发布并记录函数访问地址

项目创建完成后，执行 `pnpm faas:depoly`，将终端返回结果中的 system_url 记录到 memory\_bank 中去。

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