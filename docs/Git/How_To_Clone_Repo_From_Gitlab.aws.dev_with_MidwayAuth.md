

[toc]

## Step 1: Execute `mwinit` in your local Mac, enter the PIN and then press the midway chip. It will automatically generate a file named `id_rsa-cert.pub` in your ~/.ssh directory.

```shell
mwinit
```



## Step 2: Copy the newly generated file to your remote EC2 with the command below.

Please note that here you need to make sure the names of your private key and public key are the same, for example, `id_rsa-cert`.

```shell
# id_rsa-cert.pub is the generated cert file. 
scp -i "~/pem/TokyoKeyPair.pem" id_rsa-cert.pub ubuntu@ec2-18-183-241-51.ap-northeast-1.compute.amazonaws.com:/home/ubuntu/.ssh

# also copy the private key id_rsa to the same folder, but with a new name
scp -i "~/pem/TokyoKeyPair.pem" id_rsa ubuntu@ec2-18-183-241-51.ap-northeast-1.compute.amazonaws.com:/home/ubuntu/.ssh/id_rsa_new
```



## Step 3.1: Run the following command to clone repo from GitHub.aws.dev. 

```shell 
GIT_SSH_COMMAND='ssh -i ~/.ssh/id_rsa-cert' git clone git@ssh.gitlab.aws.dev:aws-gcr-solutions/industry-assets/cross/guidance-for-generative-bi.git
```

`GIT_SSH_COMMAND='ssh -i ~/.ssh/id_rsa-cert'` 是一种在执行 `git` 命令时，临时设置 `GIT_SSH_COMMAND` 环境变量的方式。以下是它的作用和工作原理：

   - `GIT_SSH_COMMAND` 是 Git 提供的一个环境变量，允许你在执行 Git 的 SSH 操作（如 `git clone`、`git fetch`、`git push` 等）时指定自定义的 SSH 命令。
   - 当你设置了 `GIT_SSH_COMMAND` 环境变量时，Git 会在执行需要 SSH 的操作时使用这个自定义的 SSH 命令，而不是默认的 `ssh` 命令。
   - 在你提到的例子中，`GIT_SSH_COMMAND='ssh -i ~/.ssh/id_rsa-cert'` 告诉 Git 在执行命令时使用 `ssh -i ~/.ssh/id_rsa-cert` 来连接到远程仓库。这意味着它会使用指定的私钥文件 `~/.ssh/id_rsa_cert` 进行身份验证。



## Step 3.2: If you don't want to use `GIT_SSH_COMMAND`, you can configure `~/.ssh/config` by adding below lines:

Please note here the hostname is `ssh.gitlab.aws.dev` instead of `gitlab.aws.dev`

```yaml
Host ssh.gitlab.aws.dev
  HostName ssh.gitlab.aws.dev
  User git
  IdentityFile ~/.ssh/id_rsa-cert
```

**`~/.ssh/config` 文件的作用**

   - `~/.ssh/config` 文件允许你为特定的主机配置默认的 SSH 设置，比如使用的私钥文件、用户名、端口等。
   - 如果你在 `~/.ssh/config` 中为特定的主机（如 `gitlab.aws.dev`）配置了 `IdentityFile`，SSH 会自动使用这个配置文件中的私钥进行连接，而不需要每次通过 `GIT_SSH_COMMAND` 来指定。
   - 因此，如果配置了 `~/.ssh/config` 文件，并正确指定了 `IdentityFile`，理论上不需要再使用 `GIT_SSH_COMMAND` 环境变量，因为 SSH 配置文件已经能够提供相同的功能。



## Appendix: Tools for troubleshooting

1. **验证私钥和公钥是否匹配**：
   - 使用以下命令检查私钥的指纹：
     ```bash
     ssh-keygen -lf ~/.ssh/id_rsa-cert
     ```
   - 使用以下命令检查公钥的指纹：
     ```bash
     ssh-keygen -lf ~/.ssh/id_rsa-cert.pub
     ```
   - 确保这两个指纹一致。

2. **确认 GitLab 上的 SSH 密钥配置**：
   - 登录到 GitLab，导航到用户设置中的“SSH Keys”部分，确认你正在使用的公钥已经被添加到你的 GitLab 账户中。

3. **检查 SSH 配置文件**：
   - 确保 `~/.ssh/config` 文件中的 `IdentityFile` 路径指向正确的私钥文件，并且该文件具有正确的权限：
     ```bash
     chmod 600 ~/.ssh/id_rsa-cert
     ```

4. **测试 SSH 连接**：
   - 通过以下命令测试是否能成功连接：
     ```bash
     ssh -i ~/.ssh/id_rsa-cert git@ssh.gitlab.aws.dev
     ```
   - 如果连接成功，说明私钥配置正确。然后再尝试 `git clone`。

