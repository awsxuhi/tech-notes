如果您希望使用 TypeScript 来编写自定义的 GitHub Action，您需要将 TypeScript 代码编译并打包成 JavaScript，以便 GitHub Actions 可以运行。以下是详细的步骤指南：

---

## 一、概述

虽然 GitHub Actions 最终运行的是 JavaScript，但您完全可以使用 TypeScript 编写，然后将其编译为 JavaScript。这不仅可以利用 TypeScript 的类型检查和高级特性，还可以提高代码的可维护性和可读性。

---

## 二、创建 TypeScript GitHub Action 的步骤

### 1. 初始化项目

创建一个新的目录，或者在现有的仓库中创建一个用于存放 Action 的目录。例如，创建 `.github/actions/my-action`。

```bash
mkdir -p .github/actions/my-action
cd .github/actions/my-action
```

### 2. 初始化 `package.json`

使用 `npm init` 命令初始化一个新的 Node.js 项目。

```bash
npm init -y
```

### 3. 安装必要的依赖项

安装 GitHub Actions 所需的核心库，以及 TypeScript 和打包工具。

```bash
npm install @actions/core @actions/github
npm install typescript @types/node --save-dev
npm install @vercel/ncc --save-dev
```

- `@actions/core`：GitHub Actions 的核心库，提供获取输入、设置输出等功能。
- `@actions/github`：提供 GitHub API 的封装，方便与 GitHub 进行交互。
- `typescript` 和 `@types/node`：TypeScript 及其类型定义。
- `@vercel/ncc`：用于将 TypeScript 编译并打包为一个单一的 JavaScript 文件。

### 4. 创建 `tsconfig.json`

在项目根目录下创建 `tsconfig.json` 文件，用于配置 TypeScript 编译选项。



你可以在您的项目根目录中，打开命令行工具，运行以下命令：

```
tsc --init
```

这将会在当前目录下生成一个基本的 `tsconfig.json` 文件，其中包含了 TypeScript 编译器的默认配置，和非常多的注释。但是这个文件过于基本，通常是自己根据自己的习惯生成一个tsconfig.json文件，并在未来需要的时候自己copy。



```json
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "ES2020",
    "lib": ["ES2020"],
    "outDir": "lib",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules", "**/*.test.ts"]
}
```

- **`module`**：指定生成的代码使用 CommonJS 模块系统。
- **`target`**：指定编译的目标 ECMAScript 版本。
- **`outDir`**：编译后的文件输出目录。
- **`include`**：指定要编译的 TypeScript 文件路径。

### 5. 编写 Action 代码

创建 `src` 目录，并在其中编写您的 TypeScript 代码。例如，创建 `src/index.ts`。

```bash
mkdir src
touch src/index.ts
```

在 `src/index.ts` 中编写您的 Action 逻辑：

```typescript
import * as core from '@actions/core';
import * as github from '@actions/github';

async function run(): Promise<void> {
  try {
    const myInput = core.getInput('my-input');
    core.info(`My input is: ${myInput}`);

    // 获取 GitHub 上下文
    const context = github.context;
    core.info(`Event name: ${context.eventName}`);

    // 设置输出参数
    core.setOutput('my-output', 'This is my output value');
  } catch (error) {
    if (error instanceof Error) core.setFailed(error.message);
  }
}

run();
```

- **获取输入**：使用 `core.getInput('my-input')` 获取用户传入的参数。
- **日志输出**：使用 `core.info()`、`core.warning()`、`core.error()` 输出日志信息。
- **设置输出**：使用 `core.setOutput()` 设置输出参数。
- **错误处理**：捕获异常并使用 `core.setFailed()` 标记 Action 失败。

### 6. 编写 `action.yml` 元数据文件

在项目根目录下创建 `action.yml` 文件，描述您的 Action。

```yaml
name: 'My TypeScript Action'
description: 'An example GitHub Action written in TypeScript'
inputs:
  my-input:
    description: 'An example input parameter'
    required: true
    default: 'default value'
outputs:
  my-output:
    description: 'An example output parameter'
runs:
  using: 'node16'
  main: 'dist/index.js'
```

- **`runs` 部分**：指定运行环境和入口文件。注意这里的 `main` 指向编译并打包后的文件路径 `dist/index.js`。

### 7. 添加构建脚本

在 `package.json` 中添加构建脚本，用于编译 TypeScript 并打包。

```json
{
  "scripts": {
    "build": "ncc build src/index.ts --license licenses.txt"
  }
}
```

- **`ncc build`**：使用 `@vercel/ncc` 编译并打包代码。
- **`--license`**：将所依赖的库的许可证信息输出到 `licenses.txt`。

### 8. 构建代码

运行以下命令编译并打包您的代码：

```bash
npm run build
```

这将在 `dist` 目录中生成一个 `index.js` 文件，即打包后的代码。

### 9. 提交代码

将所有文件提交到您的仓库，包括：

- `action.yml`
- `package.json` 和 `package-lock.json`
- `tsconfig.json`
- `src` 目录下的 TypeScript 源代码
- `dist` 目录下的打包后的代码

**注意**：由于打包后的代码已经包含了所有依赖，可以考虑将 `node_modules` 和 `src` 目录添加到 `.gitignore` 中，或者根据您的需求进行调整。

---

## 三、在工作流程中使用您的 TypeScript Action

在仓库的 `.github/workflows` 目录下创建或修改工作流程文件，例如 `main.yml`：

```yaml
name: 'My Workflow'

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: 检出代码
        uses: actions/checkout@v3

      - name: 运行自定义 TypeScript Action
        uses: ./github/actions/my-action
        with:
          my-input: 'Hello, TypeScript!'
```

- **`uses`**：使用本地路径引用您的 Action。

---

## 四、测试和调试

1. **提交更改**：将所有更改提交并推送到 GitHub。

2. **查看 Actions**：在仓库的 "Actions" 选项卡中，查看工作流程的运行情况。

3. **调试**：如果出现错误，可以查看运行日志，定位问题。

---

## 五、示例项目结构

```
your-repo/
├── .github/
│   ├── actions/
│   │   └── my-action/
│   │       ├── dist/
│   │       │   └── index.js
│   │       ├── src/
│   │       │   └── index.ts
│   │       ├── action.yml
│   │       ├── package.json
│   │       ├── package-lock.json
│   │       └── tsconfig.json
│   └── workflows/
│       └── main.yml
└── ...
```

---

## 六、最佳实践

### 1. 使用 `.gitignore`

在 Action 目录下创建 `.gitignore` 文件，忽略不必要的文件：

```
node_modules/
lib/
dist/
```

### 2. 发布版本

如果您希望其他人也能使用您的 Action，可以将其发布到 GitHub Marketplace，并使用 Git 标签管理版本。

```bash
git tag -a -m "Release v1" v1
git push --follow-tags
```

### 3. 编写文档

在仓库的 `README.md` 中详细说明您的 Action，包括：

- 功能描述
- 输入和输出参数
- 使用示例
- 注意事项

### 4. 错误处理

确保在代码中捕获异常，并使用 `core.setFailed()` 标记失败，以便用户在工作流程中看到错误信息。

### 5. 测试

在发布之前，充分测试您的 Action，确保其在不同的场景下都能正常运行。

---

## 七、参考资源

- [官方文档 - 使用 TypeScript 编写 JavaScript Actions](https://docs.github.com/cn/actions/creating-actions/creating-a-javascript-action#writing-the-action-code)
- [Actions 模板 - 使用 TypeScript 编写的 GitHub Action 示例](https://github.com/actions/typescript-action)
- [@actions/Toolkit](https://github.com/actions/toolkit)：GitHub Actions 官方工具包

---

## 八、总结

通过以上步骤，您可以成功地使用 TypeScript 编写自定义的 GitHub Action。TypeScript 提供了强类型和现代化的语法，使您的代码更健壮和易于维护。编译和打包步骤确保了您的 Action 可以在 GitHub Actions 环境中顺利运行。

如有任何疑问，欢迎参考官方文档或社区资源。

项目结构

```go
your-action/
├── .github/
│   └── workflows/
│       └── main.yml        // GitHub Actions 工作流程文件
├── node_modules/           // 依赖包目录
├── src/                    // TypeScript 源代码目录
│   └── index.ts
├── lib/                    // 编译后的 JavaScript 文件目录
│   └── index.js
├── .gitignore
├── action.yml              // GitHub Action 的元数据文件
├── package.json
├── package-lock.json
└── tsconfig.json

```

