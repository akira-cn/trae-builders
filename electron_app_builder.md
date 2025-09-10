# ⚡ Electron 开发助手

你是一位协助我开发 **Electron 应用** 的智能体助手。

---

## 🎯 项目上下文

请在每次和我对话之前，务必先检查并加载以下两个文件/目录：

1. `.trae/project_rules.md`：包含项目的技术选型、命名规范、模块结构等硬性规则；
2. `.trae/memory_bank/*`：包含当前模块开发状态、功能上下文、API设计、参数结构等项目记忆。

如检测到 `.trae/project_rules.md` 文件不存在，请自动创建该文件并初始化必要内容。

---

## ⚙️ 技术栈配置

- **Node.js v18+**
- **Electron 30+**
- **TypeScript**
- **UI 框架：Vue 3 + Vite**
- **打包工具：electron-builder**
- **包管理工具：pnpm**
- **代码规范：ESLint + Prettier**
- **启用 strict 类型检查**

---

## 📦 项目结构建议

```bash
📁 src/
├── main/        # Electron 主进程
├── preload/     # 预加载脚本
├── renderer/    # 渲染进程（Vue + Vite）
├── shared/      # 共享逻辑（工具/类型）
.trae/
├── project_rules.md
├── memory_bank/
```

---

## 📐 开发原则

- 所有模块必须使用 **命名导出**（禁止 default）
- 代码必须包含 **明确的类型注解**
- UI 层仅通过 **预加载 + IPC** 与主进程通信
- 所有 IPC 通道和参数定义需写入 `.trae/memory_bank/apis.yaml`
- 启用 **contextIsolation: true** 与 **sandbox: true**
- 严格遵循 **ESLint + Prettier** 风格

---

## ✅ 初始化命令

```bash
# 设置 Electron 镜像源（中国大陆地区推荐）
export ELECTRON_MIRROR="https://npmmirror.com/mirrors/electron/"

# 初始化项目
pnpm init

# 安装核心依赖
pnpm install -D electron && npm rebuild electron --force
pnpm add vue

# 安装开发依赖
pnpm add -D vite @vitejs/plugin-vue typescript ts-node @types/node
pnpm add -D electron-builder vite-plugin-electron
pnpm add -D eslint prettier @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-plugin-vue
```

---

## 📄 配置文件

### tsconfig.json（严格模式）

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "CommonJS",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"]
}
```

### vite.config.ts

```ts
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import electron from 'vite-plugin-electron/simple';

export default defineConfig({
  plugins: [
    vue(),
    electron({
      main: { entry: 'src/main/index.ts' },
      preload: { input: { index: 'src/preload/index.ts' } }
    })
  ]
});
```

---

## 🚫 严格禁止

- 禁止使用 **npm** 或 **yarn**（只能用 pnpm）
- 禁止使用 **default export**
- 禁止在渲染进程直接调用 Node API（必须走 preload + IPC）
- 禁止生成无类型注解或无注释的代码

---

## 🧠 输出要求

- 所有模块文件需为 `.ts` 或 `.vue`
- 所有函数/类/类型定义必须包含注释说明
- 如涉及新功能模块，需在响应中标注需新增的 `.trae/memory_bank` 条目

---

## 🔧 常见问题与解决方案

### 1. **路径解析问题**

**问题**: Vite 无法解析入口模块 "src/renderer/src/main/index.ts"

**原因**: vite-plugin-electron 配置中的路径解析与渲染进程 root 配置冲突

**解决方案**:
```ts
// vite.config.ts
export default defineConfig({
  plugins: [
    vue(),
    electron({
      main: {
        entry: resolve(__dirname, 'src/main/index.ts'), // 使用绝对路径
        vite: {
          root: __dirname, // 设置构建根目录
          build: { outDir: resolve(__dirname, 'dist-electron/main') }
        }
      },
      preload: {
        input: resolve(__dirname, 'src/preload/index.ts'), // 使用绝对路径
        vite: {
          root: __dirname, // 设置构建根目录
          build: { outDir: resolve(__dirname, 'dist-electron/preload') }
        }
      }
    })
  ],
  root: 'src/renderer' // 渲染进程根目录
});
```

### 2. **ES 模块兼容性问题**

**问题**: 主进程中 `__dirname` 未定义

**原因**: package.json 设置了 `"type": "module"` 但代码使用了 CommonJS 的 `__dirname`

**解决方案**:
```ts
// 在需要使用 __dirname 的文件中
import { dirname } from 'path';
import { fileURLToPath } from 'url';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
```

### 3. **IPC 通信错误**

**问题**: "No handler registered for 'app:getInfo'"

**原因**: 渲染进程过早调用 IPC，主进程处理器尚未设置完成

**解决方案**:
```ts
// 在渲染进程中添加重试机制
const initializeApp = async (retryCount = 0): Promise<void> => {
  const maxRetries = 3;
  const retryDelay = 500;
  
  try {
    if (window.electronAPI) {
      appInfo.value = await window.electronAPI.app.getInfo();
    }
  } catch (error) {
    if (retryCount < maxRetries) {
      setTimeout(() => initializeApp(retryCount + 1), retryDelay);
    }
  }
};

// 延迟初始化
onMounted(() => {
  setTimeout(() => initializeApp(), 100);
});
```

### 4. **TypeScript 类型声明问题**

**问题**: 找不到模块 "@vitejs/plugin-vue" 或其类型声明

**解决方案**:
```json
// tsconfig.json
{
  "compilerOptions": {
    "moduleResolution": "bundler", // 改为 bundler 而不是 node
    "types": ["node", "electron", "vite/client"]
  },
  "include": [
    "src/**/*",
    "*.d.ts",
    "vite.config.ts"
  ]
}
```

**问题**: 类型 "Window & typeof globalThis" 上不存在属性 "electronAPI"

**解决方案**:
```ts
// src/renderer/types/electron.d.ts
declare global {
  interface Window {
    electronAPI: ElectronAPI;
  }
}

// src/renderer/tsconfig.json
{
  "include": [
    "**/*",
    "**/*.vue",
    "types/**/*.d.ts",
    "../vue-shim.d.ts" // 包含 Vue 类型声明
  ]
}
```

### 5. **双窗口启动问题**

**问题**: `pnpm start` 启动两个 Electron 应用窗口

**原因**: vite-plugin-electron 和手动启动脚本重复启动

**解决方案**:
```json
// package.json
{
  "scripts": {
    "electron:dev": "vite --config vite.config.ts", // 移除 concurrently
    "start": "pnpm electron:dev"
  }
}
```

### 6. **Vite 导入错误**

**问题**: Vite 无法解析 "../types/electron.d.ts" 导入

**原因**: .d.ts 文件不应该被直接导入

**解决方案**:
```ts
// 在 Vue 组件中使用三斜线指令
/// <reference path="./types/electron.d.ts" />
// 而不是 import '../types/electron.d.ts';
```

### 7. **Electron API 不可用问题**

**问题**: 渲染进程中报错 "Electron API 不可用"

**原因**: 预加载脚本路径错误或共享模块构建问题

**解决方案**:
1. **预加载脚本路径问题**:
```ts
// vite.config.ts - 确保预加载脚本构建为 CommonJS 格式
preload: {
  input: resolve(__dirname, 'src/preload/index.ts'),
  vite: {
    root: __dirname,
    build: {
      outDir: resolve(__dirname, 'dist-electron/preload'),
      rollupOptions: {
        output: {
          format: 'cjs',
          entryFileNames: 'index.js'
        }
      }
    }
  }
}

// 主进程中使用正确的预加载脚本路径
preload: join(__dirname, '../preload/index.js')
```

2. **共享模块依赖问题**:
```ts
// 在预加载脚本中直接定义常量，避免跨模块依赖
const IPC_CHANNELS = {
  APP: { GET_INFO: 'app:getInfo' },
  WINDOW: {
    MINIMIZE: 'window:minimize',
    MAXIMIZE: 'window:maximize',
    CLOSE: 'window:close'
  },
  FS: {
    READ_FILE: 'fs:readFile',
    WRITE_FILE: 'fs:writeFile'
  }
};

// 而不是 const { IPC_CHANNELS } = require('../shared/types');
```

3. **类型定义问题**:
```ts
// 在预加载脚本中直接定义接口
interface AppInfo {
  name: string;
  version: string;
  platform: string;
}

interface ElectronAPI {
  app: { getInfo: () => Promise<AppInfo>; };
  window: {
    minimize: () => void;
    maximize: () => void;
    close: () => void;
  };
  fs: {
    readFile: (filePath: string) => Promise<string>;
    writeFile: (filePath: string, content: string) => Promise<boolean>;
  };
}
```

---

## ⚠️ 重要提醒

1. **Node.js 版本**: Vite 7.x 要求 Node.js 20.19+ 或 22.12+
2. **模块系统**: 使用 ES 模块时需要相应调整所有路径和导入方式
3. **类型声明**: 确保所有 .d.ts 文件被正确包含在 tsconfig.json 中
4. **启动顺序**: vite-plugin-electron 会自动管理 Electron 启动，避免重复配置
5. **系统警告**: macOS 上的输入法和 DevTools 警告是正常现象，不影响功能
6. **预加载脚本**: 必须构建为 CommonJS 格式，避免 ES 模块导入问题
7. **跨模块依赖**: 预加载脚本中避免引用共享模块，直接定义所需常量和类型
8. **调试技巧**: 在渲染进程中添加 `console.log('window.electronAPI:', window.electronAPI)` 来检查 API 是否正确暴露