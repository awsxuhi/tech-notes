### Generate SSH key so that the host can use git clone git@github.com

当Github上有一个Repo，无论它是Public还是Private的状态，都无法通过ssh的方式来克隆repo，只能通过http的方式。为了能在repo是private状态下，在一台EC2上使用git clone来克隆repo到本地，我们需要执行下面的步骤。

### 1. 检查是否已有 SSH 密钥

在 EC2 实例上运行以下命令，检查是否已经有一个 SSH 密钥：

```shell
ls -al ~/.ssh
```

如果你看到 `id_rsa.pub`（或其他以 `.pub` 结尾的文件），那么你已经有一个 SSH 密钥。

### 2. 创建 SSH 密钥（如果没有）

如果没有 SSH 密钥，你可以用以下命令创建一个：

```shell
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

按照提示进行，直到密钥创建完成。

### 3. 将 SSH 密钥添加到 SSH-agent

首先确保 SSH-agent 在后台运行：

```shell
eval "$(ssh-agent -s)"
```

然后添加你的 SSH 私钥到 SSH-agent：

```shell
ssh-add ~/.ssh/id_rsa
```

### 4. 将公钥添加到 GitHub 账户

打开 `~/.ssh/id_rsa.pub` 文件，并复制其内容。然后，去到 GitHub 账户设置的 SSH 和 GPG keys 部分，点击 "New SSH key"，粘贴你的公钥。

### 5. 测试连接

测试是否能成功连接到 GitHub：

```shell
ssh -T git@github.com
```

如果一切正常，你应该会看到一个欢迎消息。类似下面的句子：

```shell
Hi awsxuhi! You've successfully authenticated, but GitHub does not provide shell access.
```



### 6. 克隆仓库

现在你应该能够无问题地克隆你的私有仓库：

```shell
git clone git@github.com:awsxuhi/qa-bot-streamlit.git
```