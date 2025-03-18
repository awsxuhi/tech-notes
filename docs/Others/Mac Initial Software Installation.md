# Homebrew

```shell
# Install homebrew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

# brew will be in /usr/local/bin. Make sure it's in your path.
```

```shell
# Uninstall homebrew
其实只用把上面安装的install换成uninstall就行了。
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"

# Homebrew 怎么使用？常用命令有哪些？
安装软件，如：brew install oclint
卸载软件，如：brew uninstall oclint
搜索软件，如：brew search oclint
更新软件，如：brew upgrade oclint
查看安装列表， 如：brew list
更新Homebrew，如：brew update
使用brew安装wget：
brew install wget
秘籍一：使用wget更新hosts
wget https://raw.githubusercontent.com/racaljk/hosts/master/hosts -qO /tmp/hosts && sudo sh -c 'cat /tmp/hosts > /etc/hosts'
```



# ZSH

```shell
brew install zsh
chsh -s /usr/local/bin/zsh

# Install Oh-my-zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```



# vim

```shell
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim

vim ~/.vimrc

# Below are my lines in .vimrc


# exit the vim and reopen it, then install all Plugins.
vim
:PluginInstall


# Links: 
#		https://linux.cn/article-9416-1.html
#		http://www.ruanyifeng.com/blog/2018/09/vimrc.html
```

