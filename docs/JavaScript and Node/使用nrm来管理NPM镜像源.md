使用 `nrm` 管理 NPM 镜像源





`nrm`（NPM Registry Manager）主要用于快速切换 NPM 镜像源，它不直接管理 Node.js 的版本。如果你想管理 Node.js 版本，通常使用的是 `nvm`（Node Version Manager），而不是 `nrm`。但是，我可以为你介绍如何使用 `nrm` 管理 NPM 的镜像源，以及如何使用 `nvm` 来管理 Node.js 版本。

### 1. 使用 `nrm` 管理 NPM 镜像源

`nrm` 是一个简单的工具，可以用来快速在不同的 NPM 镜像源之间切换。以下是使用 `nrm` 的步骤：

#### 安装 `nrm`

```bash
npm install -g nrm
```

#### 查看可用的镜像源

```bash
nrm ls
```

输出可能类似于：

```bash
* npm ---- https://registry.npmjs.org/
  yarn ---- https://registry.yarnpkg.com/
  tencent ---- https://mirrors.cloud.tencent.com/npm/
  taobao ---- https://registry.npmmirror.com/
```

星号（`*`）表示当前正在使用的镜像源。

#### 切换镜像源

例如，在进行npm install之前切换到淘宝镜像源：

```bash
nrm use taobao
npm install
```

#### 添加自定义镜像源

你也可以添加自定义的镜像源：

```bash
nrm add <registry-name> <registry-url>
```

#### 删除自定义镜像源

```bash
nrm del <registry-name>
```

### 总结

- **`nrm`** 用于快速切换 NPM 镜像源，方便在不同的源之间切换以提高下载速度。
- **`nvm`** 用于管理不同版本的 Node.js，可以轻松切换、安装和设置默认的 Node.js 版本。

这两个工具结合使用，可以帮助你更好地管理 Node.js 和 NPM 的环境和配置，尤其在中国大陆这样的网络环境下，通过 `nrm` 使用国内的 NPM 镜像源，再结合 `nvm` 管理 Node.js 版本，会让开发效率更高。