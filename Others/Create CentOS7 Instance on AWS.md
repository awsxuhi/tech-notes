# Create CentOS7 Instance on AWS

[TOC]

After the EC2 instance was created, change the login name from `root` to `centos` when you ssh your remote instance.

`ssh -i "TokyoKeyPair.pem" centos@ec2-13-231-152-150.ap-northeast-1.compute.amazonaws.com`

After successfully SSHed, install important tools:

```shell
cat /etc/redhat-release     #See the current release

yum install -y vim git      #Install nessary software

sudo yum install packagename --downloadonly --downloaddir=/home/centos/tmp # This is to download all dependent software rpm that packagename need.
```



## Install Oh My Zsh

`cat /etc/shells` to see the current shells available in system.

```bash
yum install -y zsh
sudo chsh -s /bin/zsh centos     # without centos, it will change root's shell

git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc # Now Oh-my-zsh is enabled

# You can use below command to autoinstall oh-my-zsh to replace above 2 CLIs
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
```

Add an alias in `.zshrc` so that you can type `jpmini` to connect to your remote instance.

`alias jpmini='ssh -i "TokyoKeyPair.pem" centos@ec2-13-231-152-150.ap-northeast-1.compute.amazonaws.com'`

The next step is to change the command line prompt to include *hostname*. Run `vim .zshrc` to change the theme from *robbyrussell.zsh-theme* to *myrobbyrussell.zsh-theme*. And then:

```shell
cd .oh-my-zsh/themes
cp robbyrussell.zsh-theme myrobbyrussell.zsh-theme #Make changes to my own theme

vi myrobbyrussell.zsh-theme

```



`vi myrobbyrussell.zsh-theme`. Change **PROMPT** to below: (ref: https://bbs.archlinuxcn.org/viewtopic.php?id=2843)

```config
PROMPT='%F{255}%K{30}JPmini%f%k ${ret_status} %{$fg[cyan]%}%c%{$reset_color%} $(git_prompt_info)'
```

Run `source .zshrc`.



## SSH Keepalive

Modify */etc/profile* to make sure the ssh connection can keep alive. Add "TMOUT=0". Then in my MacPro add below 2 lines in *~/.ssh/config* file: (**This is to make sure your ssh session keepalive**)

```config
Host *
ServerAliveInterval 60
```

