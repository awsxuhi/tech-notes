



# 🧩 使用 `pyenv` + `pipx` 安装 `emd` CLI 工具的完整流程总结

------



## ✅ 第一步：切换到 `pyenv` 管理的正确 Python 版本

这个步骤不是必须的，我们假设使用pyenv来管理python版本。

我们确保当前使用的是你通过 `pyenv` 安装的某个 Python 版本（如 3.12.9），以避免使用系统的 Python。

### 🧾 命令：

```bash
pyenv install 3.12.9           # 如果尚未安装（可跳过已装的版本）
pyenv global 3.12.9            # 设置为全局默认版本
python --version               # 确认版本
```

### ✅ 运行结果：

```bash
python --version
# 输出：Python 3.12.9
```

此时 `python` 和 `pip` 指向：

```bash
~/.pyenv/versions/3.12.9/bin/python
~/.pyenv/versions/3.12.9/bin/pip
```

------



## ✅ 第二步：使用当前 Python 安装 `pipx`

`pipx` 是专门用来安装命令行工具（如 `emd`）的推荐方式。

### 🧾 命令：

```bash
python -m pip install --user pipx

// 下面这个命令可以选择安装，只是保证你的pipx在路径中
python -m pipx ensurepath
```

> 注意：安装完后可能需要重启终端或运行 `source ~/.zshrc` / `source ~/.bashrc` 让 `$PATH` 生效。

### ✅ 运行结果：

- `pipx` 主程序安装到：

  ```
  ~/.local/lib/python3.12/site-packages/pipx/
  ```

- `pipx` 命令可执行文件在：

  ```
  ~/.local/bin/pipx
  ```

- 所以你可以通过运行 `pipx --version` 来验证成功安装。

------



## ✅ 第三步：使用 `pipx` 安装 `emd` 工具

这是你的 CLI 工具包，名字为 `easy_model_deployer`，通过 pipx 安装后，用户就可以直接运行 `emd` 命令。

### 🧾 命令：

```bash
pipx install easy_model_deployer
```

### ✅ 运行结果：

- `emd` 被安装在 `pipx` 创建的专属虚拟环境中：

  ```
  ~/.local/pipx/venvs/easy_model_deployer/
  ```

- `emd` 可执行命令链接在：

  ```
  ~/.local/bin/emd
  ```

- 只要 `~/.local/bin` 在 `$PATH` 中，就可以直接运行：

  ```bash
  emd version
  ```

------



## ✅ 总结一览表

| 步骤             | 命令                                | 安装路径                                                  |
| ---------------- | ----------------------------------- | --------------------------------------------------------- |
| 切换 Python 版本 | `pyenv global 3.12.9`               | `~/.pyenv/versions/3.12.9/`                               |
| 安装 pipx        | `python -m pip install --user pipx` | `~/.local/lib/python3.12/`, CLI 在 `~/.local/bin/pipx`    |
| 安装 emd         | `pipx install easy_model_deployer`  | venv 在 `~/.local/pipx/venvs/`, CLI 在 `~/.local/bin/emd` |

------

