### 使用 `nvm` 管理 Node.js 版本

`nvm` 是一个命令行工具，用于管理多个 Node.js 版本。你可以使用 `nvm` 安装和切换不同版本的 Node.js。

#### 安装 `nvm`

在 macOS 上，可以通过以下命令安装 `nvm`：

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.4/install.sh | bash
```

安装完成后，重启终端或运行以下命令加载 `nvm`：

```bash
source ~/.nvm/nvm.sh
```

#### 查看可用的 Node.js 版本

查看所有可安装的 Node.js 版本：

```bash
nvm ls-remote
```

#### 安装特定版本的 Node.js

例如，安装 Node.js 14.x 版本：

```bash
nvm install 14
```

安装完成后，`nvm` 会自动将新安装的版本设置为当前使用的版本。

#### 切换 Node.js 版本

如果已经安装了多个版本，可以通过以下命令切换：

```bash
nvm use 14
```

#### 查看已安装的 Node.js 版本

查看当前系统中所有安装的 Node.js 版本：

```bash
nvm ls
```

#### 设置默认 Node.js 版本

你可以将某个版本设置为默认版本（在每个新终端会话中使用）：

```bash
nvm alias default 14
```

### 