## Install pipenv, pyenv on MacOs

### 方法 1：使用 Homebrew

如果你已经安装了 [Homebrew](https://brew.sh/)，那么安装 `pyenv` 就非常简单：

1. 打开终端并运行以下命令来安装 `pyenv`：

   ```shell
   brew install pyenv
   ```

2. 在你的 shell 配置文件（例如 `~/.zshrc` 对于 Zsh 或 `~/.bash_profile` 对于 Bash）中，添加以下行以初始化 `pyenv`：

   ```bash
   if command -v pyenv 1>/dev/null 2>&1; then
     eval "$(pyenv init --path)"
   fi
   ```

3. 将下面的片段加入到~/.zshrc

   ```shell
   # >>> for pyenv's init >>>
   if command -v pyenv 1>/dev/null 2>&1; then
     eval "$(pyenv init --path)"
   fi
   # <<< for pyenv's init <<< (xuhi@'s input, the first line was recommended by chatgpt)
   export PYENV_ROOT="$HOME/.pyenv"
   ```

   

4. 重新加载你的 shell 配置：

   ```bash
   source ~/.zshrc  # 或 source ~/.bash_profile
   ```

### 方法 2：手动安装

如果你不想使用 Homebrew，也可以手动安装：

1. 克隆 `pyenv` 仓库：

   ```
   git clone https://github.com/pyenv/pyenv.git ~/.pyenv
   ```

2. 在你的 shell 配置文件中，添加以下环境变量和初始化脚本：

   ```
   export PYENV_ROOT="$HOME/.pyenv"
   export PATH="$PYENV_ROOT/bin:$PATH"
   eval "$(pyenv init --path)"
   ```

3. 重新加载你的 shell 配置：

   ```
   source ~/.zshrc  # 或 source ~/.bash_profile
   ```

### 验证安装

无论你选择哪种方法，都可以通过运行以下命令来验证 `pyenv` 是否成功安装：

```
pyenv --version
```

如果你看到了版本号，那么 `pyenv` 应该已经成功安装。

这样，你就可以使用 `pyenv` 来管理不同版本的 Python 了。例如，要安装一个新版本的 Python，你可以运行：

```shell
pyenv install 3.9.1
```

然后，你可以用 `pyenv global` 或 `pyenv local` 命令来设置要使用的 Python 版本。