# AAStar
**Deprecated🛑❤️‍🩹👶**

merge into [https://github.com/AAStarCommunity/AAStar_SDK](https://github.com/AAStarCommunity/AAStar_SDK)

AAStar npm packages collection:
You can use 
```
pnpm install @aastar/airaccount
@aastar/superpaymaster
@aastar/cometens
@aastar/openpnts
@aastar/opencards
@aastar/arcadia
@aastar/cos72
```
or 
```
pnpm install aastar
```

## For developers
Why do you install this package?
### airaccount
Provide a moveable\self-custody\crypto account with life cycle service.
Permissionless.
Trustless.
Decentralized.
In 3 steps: bind, send, claim, move(recover)

### superpaymaster
Embbeded into AirAccount, but provide ERC20 gas token ability for any community individually.

### cometens
Embbeded into AirAccount, but provide set your own ENS name for your community individually.


# 从零开始开发和发布 npm 包：完整指南

## 目录

1. [前期准备](#前期准备)
2. [初始化项目](#初始化项目)
3. [配置开发环境](#配置开发环境)
4. [开发 SDK](#开发-sdk)
5. [测试](#测试)
6. [构建和打包](#构建和打包)
7. [发布到 npm](#发布到-npm)
8. [文档和示例](#文档和示例)
9. [版本管理和更新](#版本管理和更新)
10. [持续集成和自动发布](#持续集成和自动发布)
11. [SDK 开发最佳实践](#sdk-开发最佳实践)
12. [问题排查](#问题排查)

## 前期准备

### 1. 安装必要工具

```bash
# 安装 Node.js (推荐使用 nvm 管理 Node.js 版本)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
nvm install node # 安装最新版
nvm install --lts # 或安装长期支持版

# 确认安装
node --version
npm --version

# 安装一些全局工具
npm install -g typescript ts-node np
```

### 2. 创建 npm 账号

1. 访问 [npm 官网](https://www.npmjs.com/) 并注册
2. 在命令行登录 npm (后续发布时需要)

```bash
npm login
```

### 3. 规划你的 SDK

在开始编码前，确定以下内容：

- SDK 的目标和用途
- 核心功能和 API 设计
- 依赖关系
- 支持的平台 (浏览器、Node.js、React Native 等)
- 命名规范 (包名必须唯一)
- 核心 API 结构

## 初始化项目

### 1. 创建项目目录

```bash
# 创建项目目录 (使用你希望的包名)
mkdir my-awesome-sdk
cd my-awesome-sdk
```

### 2. 初始化 npm 项目

```bash
npm init
```

按照提示填写信息：

- `name`: 包名 (可以使用 `@组织名/包名` 格式)
- `version`: 版本号 (推荐从 0.1.0 开始)
- `description`: 简短描述
- `main`: 入口文件 (通常是 `dist/index.js`)
- `scripts`: 构建脚本
- `repository`: 代码仓库地址
- `keywords`: 关键词，帮助用户发现你的包
- `author`: 作者信息
- `license`: 许可证 (如 MIT)

### 3. 初始化 Git 仓库

```bash
git init
echo "node_modules\ndist\n.env\n*.log" > .gitignore
```

### 4. 创建基本目录结构

```bash
mkdir src tests examples docs
touch src/index.ts README.md LICENSE
```

## 配置开发环境

### 1. 配置 TypeScript

```bash
# 安装 TypeScript
npm install typescript --save-dev

# 初始化 TypeScript 配置
npx tsc --init
```

编辑 `tsconfig.json`：

```json
{
    "compilerOptions": {
        "target": "es2018",
        "module": "commonjs",
        "declaration": true,
        "outDir": "./dist",
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
        "forceConsistentCasingInFileNames": true,
        "rootDir": "./src"
    },
    "include": ["src"],
    "exclude": ["node_modules", "tests", "examples", "dist"]
}
```

### 2. 配置代码格式化和 Lint

```bash
# 安装 ESLint 和 Prettier
npm install eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin prettier eslint-config-prettier eslint-plugin-prettier --save-dev

# 创建 ESLint 配置
```

创建 `.eslintrc.js`：

```javascript
module.exports = {
    parser: "@typescript-eslint/parser",
    extends: [
        "plugin:@typescript-eslint/recommended",
        "plugin:prettier/recommended",
    ],
    parserOptions: {
        ecmaVersion: 2018,
        sourceType: "module",
    },
    rules: {
        // 自定义规则
    },
};
```

创建 `.prettierrc`：

```json
{
    "singleQuote": true,
    "trailingComma": "es5",
    "printWidth": 100,
    "tabWidth": 2,
    "semi": true
}
```

### 3. 配置测试环境

```bash
# 安装 Jest
npm install jest ts-jest @types/jest --save-dev
```

创建 `jest.config.js`：

```javascript
module.exports = {
    preset: "ts-jest",
    testEnvironment: "node",
    testMatch: ["**/tests/**/*.test.ts"],
    collectCoverage: true,
    coverageDirectory: "coverage",
    collectCoverageFrom: ["src/**/*.ts"],
};
```

### 4. 更新 package.json 脚本

编辑 `package.json` 添加以下脚本：

```json
"scripts": {
  "build": "tsc",
  "test": "jest",
  "lint": "eslint 'src/**/*.ts'",
  "format": "prettier --write 'src/**/*.ts'",
  "prepare": "npm run build",
  "prepublishOnly": "npm test && npm run lint",
  "preversion": "npm run lint",
  "version": "npm run format && git add -A src",
  "postversion": "git push && git push --tags"
}
```

## 开发 SDK

### 1. 创建基本结构

在 `src/index.ts` 创建 SDK 的入口点：

```typescript
// 导出所有公共 API
export * from "./client";
export * from "./types";
// ... 其他导出

// 导出默认客户端
import { Client } from "./client";
export default Client;
```

### 2. 创建核心模块

例如，创建 `src/client.ts` 实现主要客户端类：

```typescript
import { ApiResponse, ClientOptions } from "./types";

export class Client {
    private apiKey: string;
    private baseUrl: string;

    constructor(options: ClientOptions) {
        this.apiKey = options.apiKey;
        this.baseUrl = options.baseUrl || "https://api.example.com/v1";
    }

    async makeRequest<T>(
        endpoint: string,
        method: string = "GET",
        data?: any,
    ): Promise<ApiResponse<T>> {
        const url = `${this.baseUrl}/${endpoint}`;

        try {
            const response = await fetch(url, {
                method,
                headers: {
                    "Content-Type": "application/json",
                    "Authorization": `Bearer ${this.apiKey}`,
                },
                body: data ? JSON.stringify(data) : undefined,
            });

            const responseData = await response.json();

            if (!response.ok) {
                throw new Error(responseData.message || "API request failed");
            }

            return {
                data: responseData,
                status: response.status,
                headers: response.headers,
            };
        } catch (error) {
            throw error;
        }
    }

    // 公共 API 方法
    async getUser(userId: string) {
        return this.makeRequest<any>(`users/${userId}`);
    }

    // 更多 API 方法...
}
```

### 3. 定义类型

创建 `src/types.ts` 定义类型：

```typescript
export interface ClientOptions {
    apiKey: string;
    baseUrl?: string;
}

export interface ApiResponse<T> {
    data: T;
    status: number;
    headers: Headers;
}

// 更多类型定义...
```

### 4. 使用模块化设计

将 SDK 拆分为多个模块，例如：

```
src/
  index.ts          # 主入口
  client.ts         # 核心客户端类
  types.ts          # 类型定义
  modules/
    auth.ts         # 认证相关功能
    users.ts        # 用户相关功能
    products.ts     # 产品相关功能
  utils/
    request.ts      # 请求工具
    validation.ts   # 验证工具
```

## 测试

### 1. 编写单元测试

在 `tests` 目录中创建测试文件，例如 `tests/client.test.ts`：

```typescript
import { Client } from "../src/client";

// 模拟 fetch API
global.fetch = jest.fn();

describe("Client", () => {
    let client: Client;

    beforeEach(() => {
        client = new Client({ apiKey: "test-api-key" });
        (global.fetch as jest.Mock).mockClear();
    });

    test("makeRequest should call fetch with correct parameters", async () => {
        (global.fetch as jest.Mock).mockResolvedValueOnce({
            ok: true,
            status: 200,
            json: async () => ({ id: "123", name: "Test User" }),
            headers: new Headers(),
        });

        const result = await client.makeRequest("users/123");

        expect(global.fetch).toHaveBeenCalledWith(
            "https://api.example.com/v1/users/123",
            expect.objectContaining({
                method: "GET",
                headers: expect.objectContaining({
                    "Authorization": "Bearer test-api-key",
                }),
            }),
        );

        expect(result.data).toEqual({ id: "123", name: "Test User" });
    });

    // 更多测试...
});
```

### 2. 运行测试

```bash
npm test
```

### 3. 编写集成测试

创建与真实 API 交互的集成测试，例如 `tests/integration.test.ts`：

```typescript
import { Client } from "../src/client";

// 这些测试需要一个有效的 API 密钥
// 通常通过环境变量提供
const apiKey = process.env.API_KEY;

// 只在提供 API 密钥时运行集成测试
(apiKey ? describe : describe.skip)("Integration tests", () => {
    let client: Client;

    beforeAll(() => {
        client = new Client({ apiKey });
    });

    test("can get a user", async () => {
        const result = await client.getUser("test-user-id");
        expect(result.status).toBe(200);
        expect(result.data).toHaveProperty("id");
    });

    // 更多集成测试...
});
```

## 构建和打包

### 1. 配置 npm 包文件

编辑 `package.json` 指定要包含的文件：

```json
{
    "main": "dist/index.js",
    "types": "dist/index.d.ts",
    "files": [
        "dist",
        "LICENSE",
        "README.md"
    ]
}
```

### 2. 支持不同的模块格式 (可选)

如果你想同时支持 CommonJS 和 ES 模块，可以使用 `rollup` 或 `tsup` 等工具。

使用 `tsup` 示例：

```bash
# 安装 tsup
npm install tsup --save-dev
```

更新 `package.json`：

```json
{
    "main": "dist/index.js",
    "module": "dist/index.mjs",
    "types": "dist/index.d.ts",
    "exports": {
        ".": {
            "require": "./dist/index.js",
            "import": "./dist/index.mjs",
            "types": "./dist/index.d.ts"
        }
    },
    "scripts": {
        "build": "tsup src/index.ts --format cjs,esm --dts --clean"
    }
}
```

## 发布到 npm

### 1. 准备发布

确保你的包名是唯一的，并已登录 npm。如果使用组织名称，确保已创建组织。

```bash
# 检查是否登录
npm whoami

# 如果未登录
npm login
```

### 2. 测试包

在发布前，使用 `npm pack` 创建一个本地包进行测试：

```bash
npm pack
```

这将创建一个 `.tgz` 文件，可以在另一个项目中使用它进行测试：

```bash
# 在测试项目中
npm install ../path/to/your-package-1.0.0.tgz
```

### 3. 发布包

```bash
# 首次发布
npm publish

# 如果是组织包并想设为公共
npm publish --access public
```

### 4. 更新包

修改 `package.json` 中的版本号或使用 `npm version` 命令：

```bash
# 更新补丁版本 (1.0.0 -> 1.0.1)
npm version patch

# 更新小版本 (1.0.0 -> 1.1.0)
npm version minor

# 更新大版本 (1.0.0 -> 2.0.0)
npm version major
```

然后发布更新：

```bash
npm publish
```

## 文档和示例

### 1. 编写 README

一个好的 README 应包含：

- 项目描述
- 安装指南
- 基本用法示例
- API 文档链接
- 配置选项
- 常见问题

示例 README 结构：

````markdown
# My Awesome SDK

A JavaScript/TypeScript SDK for interacting with the Example API.

## Installation

```bash
npm install my-awesome-sdk
```
````

## Quick Start

```javascript
import Client from "my-awesome-sdk";

const client = new Client({ apiKey: "your-api-key" });

// Get a user
client.getUser("user123")
    .then((response) => console.log(response.data))
    .catch((error) => console.error(error));
```

## Documentation

For full documentation, visit [docs.example.com](https://docs.example.com).

## Configuration

The client accepts the following options:

| Option  | Type   | Required | Default                    | Description      |
| ------- | ------ | -------- | -------------------------- | ---------------- |
| apiKey  | string | Yes      | -                          | Your API key     |
| baseUrl | string | No       | https://api.example.com/v1 | The API base URL |

## License

MIT

````
### 2. 示例代码

在 `examples` 目录中创建示例代码：

```typescript
// examples/basic-usage.ts
import Client from '../src';

async function main() {
  const client = new Client({ apiKey: 'your-api-key' });
  
  try {
    const user = await client.getUser('user123');
    console.log('User:', user.data);
    
    // More examples...
  } catch (error) {
    console.error('Error:', error);
  }
}

main();
````

### 3. 生成 API 文档

可以使用 TypeDoc 生成 API 文档：

```bash
npm install typedoc --save-dev
```

添加 `package.json` 脚本：

```json
"scripts": {
  "docs": "typedoc --out docs src/index.ts"
}
```

## 版本管理和更新

### 1. 遵循语义化版本规范

- **补丁版本**（1.0.x）：修复 bug，小改进
- **小版本**（1.x.0）：添加向后兼容的新功能
- **大版本**（x.0.0）：不向后兼容的变更

### 2. 维护更新日志

创建 `CHANGELOG.md` 并记录每个版本的变更：

```markdown
# Changelog

## [1.1.0] - 2023-05-01

### Added

- New feature X
- Support for Y

### Changed

- Improved error handling
- Better performance for Z

### Fixed

- Bug in getUser method

## [1.0.0] - 2023-04-01

Initial release
```

## 持续集成和自动发布

### 1. 设置 GitHub Actions

创建 `.github/workflows/ci.yml`：

```yaml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [14.x, 16.x, 18.x]
    
    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
    - run: npm ci
    - run: npm run build
    - run: npm test
    - run: npm run lint
```

### 2. 设置自动发布

创建 `.github/workflows/publish.yml`：

```yaml
name: Publish

on:
  release:
    types: [created]

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - uses: actions/setup-node@v3
      with:
        node-version: '16.x'
        registry-url: 'https://registry.npmjs.org'
    - run: npm ci
    - run: npm run build
    - run: npm test
    - run: npm publish
      env:
        NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

在 GitHub 仓库设置中，添加 `NPM_TOKEN` 秘密。

## SDK 开发最佳实践

### 1. API 设计原则

- **简单直观**：API 应该易于理解和使用
- **一致性**：保持命名和模式一致
- **错误处理**：提供清晰的错误信息和类型
- **文档完善**：每个公共方法都应有文档
- **链式调用**：考虑支持方法链接

### 2. 错误处理

创建自定义错误类：

```typescript
// src/errors.ts
export class ApiError extends Error {
    statusCode: number;
    data: any;

    constructor(message: string, statusCode: number, data?: any) {
        super(message);
        this.name = "ApiError";
        this.statusCode = statusCode;
        this.data = data;
        Object.setPrototypeOf(this, ApiError.prototype);
    }
}

export class ValidationError extends Error {
    errors: any[];

    constructor(message: string, errors: any[]) {
        super(message);
        this.name = "ValidationError";
        this.errors = errors;
        Object.setPrototypeOf(this, ValidationError.prototype);
    }
}
```

### 3. 浏览器兼容性

如果 SDK 需要在浏览器中运行，考虑：

- 使用 `fetch` 时提供兼容性封装
- 考虑使用 `axios` 等库处理请求
- 使用 polyfills 确保兼容性

### 4. 安全性

- 不要在客户端代码中存储敏感信息
- 使用 HTTPS
- 避免将敏感信息记录到控制台
- 提供 API 令牌轮换机制

## 问题排查

### 1. 常见问题

- **包名冲突**：在 npm 上查找是否已存在同名包
- **构建错误**：检查 TypeScript 配置和依赖
- **发布失败**：确保已登录 npm 并有权限发布

### 2. 调试技巧

```typescript
// 添加调试配置
export class Client {
    private debug: boolean;

    constructor(options: ClientOptions) {
        this.debug = options.debug || false;
        // ...
    }

    private log(...args: any[]) {
        if (this.debug) {
            console.log("[SDK]", ...args);
        }
    }

    async makeRequest<T>(
        endpoint: string,
        method: string = "GET",
        data?: any,
    ): Promise<ApiResponse<T>> {
        this.log("Request:", method, endpoint, data);

        // ...请求逻辑...

        this.log("Response:", responseData);
        return response;
    }
}
```

### 3. 提供用户支持

- 在 GitHub 上设置 Issues 模板
- 提供常见问题和解决方案
- 考虑设置讨论区或社区渠道

# NPM包组织

您想要建立一个名为"aastar"的NPM组织，其中包含多个子包，既可以单独安装也可以整体安装。这是一个非常好的组织方式，类似于许多流行的库如Next.js、Ant
Design等采用的结构。

要实现这样的结构，您需要采用"Monorepo"(单体仓库)架构，以下是详细的规划和实现步骤：

## 1. 创建Monorepo项目结构

首先，建立基本的项目结构：

```
aastar/
├── packages/
│   ├── airaccount/        # 子包1
│   ├── superpaymaster/    # 子包2
│   └── ...                # 其他子包
├── package.json           # 根项目配置
├── pnpm-workspace.yaml    # 工作区配置
├── lerna.json             # (可选)Lerna配置
└── tsconfig.json          # 基础TypeScript配置
```

## 2. 配置工作区

在根目录创建`pnpm-workspace.yaml`文件：

```yaml
packages:
  - 'packages/*'
```

## 3. 配置每个子包

在每个子包目录中创建`package.json`，例如`packages/airaccount/package.json`：

```json
{
    "name": "@aastar/airaccount",
    "version": "0.1.0",
    "description": "AIR账户管理模块",
    "main": "dist/index.js",
    "types": "dist/index.d.ts",
    "scripts": {
        "build": "tsc",
        "test": "jest"
    },
    "dependencies": {
        // 此包的依赖
    },
    "peerDependencies": {
        // 可选的同级依赖
    },
    "publishConfig": {
        "access": "public"
    }
}
```

## 4. 创建主包作为入口点

在`packages`目录下创建一个集合包，例如`packages/aastar/package.json`：

```json
{
    "name": "aastar",
    "version": "0.1.0",
    "description": "AASTAR SDK全集合",
    "main": "dist/index.js",
    "types": "dist/index.d.ts",
    "scripts": {
        "build": "tsc"
    },
    "dependencies": {
        "@aastar/airaccount": "workspace:*",
        "@aastar/superpaymaster": "workspace:*"
        // 添加其他所有子包作为依赖
    },
    "publishConfig": {
        "access": "public"
    }
}
```

然后创建相应的入口文件`packages/aastar/src/index.ts`，导出所有子包：

```typescript
export * from "@aastar/airaccount";
export * from "@aastar/superpaymaster";
// 导出其他所有子包
```

## 5. 使用工具管理版本和发布

您可以使用以下工具之一来管理版本和发布：

### 选项1: 使用pnpm + changesets

1. 安装changesets：

```bash
pnpm add -D -w @changesets/cli
pnpm changeset init
```

2. 配置发布脚本：在根目录的`package.json`中添加：

```json
{
    "scripts": {
        "build": "pnpm -r build",
        "version": "changeset version",
        "publish": "pnpm build && changeset publish"
    }
}
```

### 选项2: 使用Lerna

1. 安装Lerna：

```bash
pnpm add -D -w lerna
```

2. 创建`lerna.json`：

```json
{
    "version": "independent",
    "npmClient": "pnpm",
    "useWorkspaces": true,
    "command": {
        "publish": {
            "ignoreChanges": ["*.md"],
            "message": "chore(release): publish"
        }
    }
}
```

## 6. 处理包之间的依赖关系

如果子包之间有依赖关系，在依赖包的`package.json`中添加：

```json
{
    "dependencies": {
        "@aastar/some-dependency": "workspace:*"
    }
}
```

## 7. 统一配置和构建

创建一个根目录的`tsconfig.json`作为基础配置：

```json
{
    "compilerOptions": {
        "target": "ES2020",
        "module": "ESNext",
        "moduleResolution": "node",
        "declaration": true,
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
        "forceConsistentCasingInFileNames": true
    }
}
```

然后，每个子包可以扩展此配置：

```json
{
    "extends": "../../tsconfig.json",
    "compilerOptions": {
        "outDir": "./dist",
        "rootDir": "./src"
    },
    "include": ["src/**/*"]
}
```

## 8. 发布流程

1. 创建npm组织：
   - 在npm网站上注册`aastar`组织

2. 发布所有包：

```bash
# 使用changesets
pnpm changeset
pnpm version
pnpm publish

# 或使用Lerna
pnpm lerna publish
```

## 使用示例

用户可以按您希望的方式安装和使用SDK：

```bash
# 安装单个包
pnpm install @aastar/airaccount @aastar/superpaymaster

# 或安装全部功能
pnpm install aastar
```

使用示例：

```javascript
// 单个包使用方式
import { someFunction } from "@aastar/airaccount";

// 全部功能使用方式
import { airaccount, superpaymaster } from "aastar";
```

这种结构的主要优点是：

1. 用户可以根据需求选择安装单个包或全部功能
2. 包之间可以共享代码，但又保持独立发布
3. 可以统一管理版本，简化发布流程
4. 便于维护和扩展

您对这种结构有什么特别的需求或问题吗？

