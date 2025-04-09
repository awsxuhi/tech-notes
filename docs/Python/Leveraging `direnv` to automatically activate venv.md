# 在 macOS 上配置 direnv，实现进入项目目录自动激活虚拟环境（使用 uv venv）

## 前置要求

- macOS 系统
- 使用 zsh 作为默认 shell（mac 默认即是）
- 使用 Oh My Zsh 管理 zsh 插件
- 已安装 [uv](https://github.com/astral-sh/uv)（用于创建虚拟环境）

---

## 第一步：安装 direnv

使用 Homebrew 安装 direnv：

```
brew install direnv

# 在ubuntu上使用下面的命令安装:
sudo apt update
sudo apt install direnv
```

---

## 第二步：配置 Oh My Zsh 支持 direnv

1. 打开 `~/.zshrc` 文件：

   ```
   code ~/.zshrc
   ```

   或使用其他编辑器：

   ```
   nano ~/.zshrc
   ```

2. 找到 `plugins=(...)` 这一行，添加 `direnv` 插件，例如：

   ```
   plugins=(
     git
     direnv
     zsh-syntax-highlighting
     zsh-autosuggestions
   )
   ```

3. 保存后重新加载配置：

   ```
   source ~/.zshrc
   ```

说明：Oh My Zsh 的 `direnv` 插件已经自动包含了 `eval "$(direnv hook zsh)"`，无需手动添加。如果没有使用 Oh My Zsh，可以手动添加`eval "$(direnv hook zsh)"`到.zshrc。

---

## 第三步：在项目中使用 uv 创建虚拟环境

1. 创建并进入项目目录：

```
mkdir my-project
cd my-project
```

1. 使用 uv 创建虚拟环境：

```
uv venv
```

说明：

- 该命令会在当前目录创建一个名为 `.venv` 的虚拟环境文件夹；
- 不需要传入路径参数；
- 不会生成 `__pypackages__`；
- 如果运行了 Python 脚本，会自动生成 `__pycache__`，这是正常行为。

⚠️ 注意：**`uv venv` 是用于创建虚拟环境的，不要在每次进入目录时执行，否则会覆盖已有的环境。**

---

## 第四步：配置 direnv 实现自动激活虚拟环境

1. 在项目根目录创建 `.envrc` 文件，添加激活命令：

```
echo 'source .venv/bin/activate' > .envrc
```

1. 授权当前目录的 `.envrc` 文件：

```
direnv allow
```

---

## 效果验证

每次进入该项目目录时，终端将自动激活虚拟环境，并显示类似提示：

```
direnv: loading .envrc
direnv: export +VIRTUAL_ENV ...
```

你可以通过以下命令验证虚拟环境是否生效：

```
which python
```

应输出 `.venv/bin/python` 路径。

---

## 离开目录后自动退出虚拟环境

当你离开该项目目录时，direnv 会自动卸载相关环境变量，虚拟环境自动失效，无需手动执行 `deactivate`。

---

## 如果不使用 direnv，如何手动激活虚拟环境？

每次进入项目目录后，执行：

```
source .venv/bin/activate
```

退出虚拟环境时执行：

```
deactivate
```

⚠️ 注意：**不要使用 `uv venv` 作为激活方式**，它只用于创建虚拟环境，不能重复执行。

---

## 其他实用命令

- 修改 `.envrc` 文件后重新授权：

  ```
  direnv allow

  或者
  direnv allow .
  ```

- 查看当前目录的 direnv 状态：

  ```
  direnv status
  ```

- 撤销当前目录的授权：

  ```
  direnv deny
  ```

---

## 小结

通过配置 direnv 与 uv，可以实现项目级的虚拟环境自动激活与清理。这样不仅避免了全局污染，还可以大幅提升开发效率，尤其适用于多项目并行开发的场景。

如果你希望进一步自动化，也可以将 `.envrc` 的创建与 `uv venv` 一并封装成一个脚本，方便新项目初始化。
