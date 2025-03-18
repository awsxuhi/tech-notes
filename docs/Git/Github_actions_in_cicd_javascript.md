编写一个 GitHub Actions 可以帮助您自动化软件开发工作流程，如构建、测试和部署。以下是如何编写一个自定义 GitHub Action 的详细指南。

---

## 一、了解 GitHub Actions

GitHub Actions 是一个持续集成和持续部署（CI/CD）平台，允许您在 GitHub 仓库中自动化软件开发工作流程。通过编写自定义的 Actions，您可以创建可重用的组件，以简化重复性的任务。

---

## 二、创建一个自定义 GitHub Action

### 1. 创建一个新的仓库或使用现有仓库

- **新建仓库**：如果您希望将 Action 独立维护，建议创建一个新的公共仓库。
- **使用现有仓库**：如果 Action 只在当前项目中使用，可以直接在现有仓库中创建。

### 2. 目录结构

在仓库中，创建一个用于存放 Action 的目录，例如 `.github/actions/my-action`。

```
your-repo/
├── .github/
│   └── actions/
│       └── my-action/
│           ├── action.yml
│           └── index.js
└── ...
```

### 3. 编写 `action.yml` 文件

`action.yml` 是 Action 的元数据文件，定义了 Action 的输入、输出和运行环境。以下是一个基本的 `action.yml` 模板：

```yaml
name: 'My Custom Action'
description: '描述您的 Action 功能'
inputs:
  my-input:
    description: '输入参数的描述'
    required: true
    default: 'default value'
outputs:
  my-output:
    description: '输出参数的描述'
runs:
  using: 'node16'
  main: 'index.js'
```

- **name**：Action 的名称。
- **description**：对 Action 的简要描述。
- **inputs**：定义 Action 所需的输入参数。
- **outputs**：定义 Action 的输出参数。
- **runs**：指定运行环境和入口文件。

### 4. 编写 Action 的代码

根据 `action.yml` 中的配置，编写 `index.js` 文件。以下是一个简单的示例，使用 JavaScript 编写一个打印输入参数的 Action。

```javascript
const core = require('@actions/core');

try {
  const myInput = core.getInput('my-input');
  console.log(`My input is: ${myInput}`);

  // 设置输出参数
  core.setOutput('my-output', 'This is my output value');
} catch (error) {
  core.setFailed(`Action failed with error ${error}`);
}
```

- **@actions/core**：GitHub 提供的官方库，方便获取输入参数和设置输出参数。

### 5. 设置依赖和打包（如果需要）

如果您的 Action 依赖于外部包，需要创建一个 `package.json` 文件并安装依赖：

```bash
npm init -y
npm install @actions/core @actions/github @actions/exec
```

对于 JavaScript Actions，通常需要将代码编译或打包，以确保所有依赖都包含在内。您可以使用 `ncc` 进行打包：

```bash
npm install -g @vercel/ncc
ncc build index.js --license licenses.txt
```

这将在 `dist` 目录中生成打包后的文件，并更新 `action.yml`：

```yaml
runs:
  using: 'node16'
  main: 'dist/index.js'
```

---

## 三、在工作流程中使用自定义 Action

创建或修改 `.github/workflows` 目录下的工作流程文件（如 `main.yml`）：

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

      - name: 运行自定义 Action
        uses: ./github/actions/my-action
        with:
          my-input: 'Hello, World!'
```

- **uses**：指定使用的 Action。如果是本地路径，使用相对路径；如果是公共仓库的 Action，使用 `owner/repo@version` 的格式。

---

## 四、测试您的 Action

1. **提交代码**：将所有更改提交到仓库。

2. **查看运行结果**：在 GitHub 仓库的 "Actions" 标签下，可以查看工作流程的运行情况。

3. **调试**：如果运行失败，可以查看日志信息，进行调试和修改。

---

## 五、发布和共享您的 Action

如果您希望分享您的 Action，可以将仓库设为公共，并在 `action.yml` 中添加 `branding` 信息：

```yaml
branding:
  color: 'blue'
  icon: 'terminal'
```

然后，其他人可以通过引用您的仓库来使用您的 Action：

```yaml
uses: your-username/your-action-repo@v1
```

---

## 六、最佳实践

- **版本控制**：使用 Git 标签来管理 Action 的版本，方便用户指定特定版本。

- **文档**：在仓库的 `README.md` 中详细说明 Action 的用途、输入输出参数和使用示例。

- **错误处理**：确保在代码中正确处理错误，并使用 `core.setFailed()` 方法标记失败。

- **安全性**：避免在代码中暴露敏感信息，如密钥或令牌。

---

## 七、参考资源

- [官方文档 - 创建自定义 JavaScript Actions](https://docs.github.com/cn/actions/creating-actions/creating-a-javascript-action)
- [Actions 入门教程](https://docs.github.com/cn/actions/learn-github-actions/understanding-github-actions)
- [GitHub Actions 市场](https://github.com/marketplace?type=actions)

---

通过以上步骤，您可以成功编写并使用一个自定义的 GitHub Action，以自动化您的开发流程。如有任何疑问，欢迎参考官方文档或社区资源。