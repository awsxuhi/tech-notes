# git

Install git on Mac. Navigate to https://www.git-scm.com/ and download the *.dmg installation file.

Double click the dmg file to install git in Mac.



## git config

Now it's our turn to do the initial configuration. [Detailed doc is in git-scm]([https://git-scm.com/book/zh/v1/%E8%B5%B7%E6%AD%A5-%E5%88%9D%E6%AC%A1%E8%BF%90%E8%A1%8C-Git-%E5%89%8D%E7%9A%84%E9%85%8D%E7%BD%AE](https://git-scm.com/book/zh/v1/起步-初次运行-Git-前的配置))

```shell
# Below --global is to configure ~/.gitconfig; use --system to configure /etc/gitconfig; without any option, it will configure .gitconfig in working directory.

git config --global user.name "Henry Xu"
git config --global user.email "xuhi@amazon.com"
```



## git init

To initialize a new git directory:

```shell
cd /tmp
git init testgit # it will create a new directory with .git in it.

#above too commands equals to below 2 commands
cd /tmp/testgit
git init

ls -la .git
more .git/config

# below will configure .git/config in local directory
git config user.name "Henry Xu"
git config user.email "xuhi@amazon.com"
```



## git add/status/commit



```shell
cd /tmp/testgit
touch test.txt
git add test.txt
git status

# Now .git/index was created. When you execute git add, all the changes(diff) will be in index file.

echo "test test" >> test.txt
git add test.txt # run it again, otherwise the changes won't be committed.
git status
git commit -m "first commit"    # Commit to local. "root-commit"=the 1st commit

touch demo.txt
echo "demo demo" >> test.txt
# Now there is a new empty file demo.txt and a changed file.
git add text.txt demo.txt
git status
git commit -m "second commit"
git log # to check 1st and 2nd commit
```



## How to ignore file

1st method: use .git/info/exclude, add below lines to exclude *.out files.

```config
*.out
```

2nd method: use .gitignore. It's suitable for many people sharing the same git repo.

```shell
vi .gitignore

# add line
*.out

git add .gitignore
```

if a.out is existed before you edit .gitignore. The file will not be ignored. In this case, you need to run below command `git rm --cached *.out` to manually ignore.

```shell
touch a.py
git add a.py
git status
echo "*.py" >> .gitignore
git status #a.py is still showed as new file.
touch b.py
git status #b.py is not showed

git rm --cached a.py
git status #a.py was removed.

```

There is a collection of useful .gitignore templates in [GitHub/gitignore](https://github.com/github/gitignore). (for C, python and etc.)

## git reset

```shell
git log
echo "hehe hehe" >> demo.txt
git status
git add demo.txt
git commit -m "edit modified demo.txt"

git log
git reset --hard HEAD~ # rollback to the previous step
git log

# If we use --soft, modify is kept. rollback to previous step.
# --soft = -- hard + git add demo.txt
git reset --soft HEAD~
git status

# If you don't use any option
# no --soft = --
git reset HEAD~

git commit -c ORIG_HEAD

#to modify the last commit, in this case, the number of commit won't change.
git commit --amend 

```

Know below concepts:

Local repository <--> Staging Area <--> Working Directory

```shell
git add xxx.txt   # add file from working directory to staging area
git commit -m "xxx"   # add file from staging area to local repository

git reset bca9fe9
# after that, the modified file is in working directory, you can git add *

git reset current~2

# Below 2 lines are executing the same.
git reset ....
git reset --mixed ....
```



Git reset --mixed(modified file is in working dir) <-> soft(modified file is in working directory, at the same time it's added to staging area. You can run git commit now) <--> hard (file back to the original file. any changes are lost)



## git remote

You need to use `git clone` before you can `git push -u origin master`. 

```shell
Mac➜  testhehe git:(master) ✗ git push -u origin master
Warning: Permanently added the RSA host key for IP address '52.192.72.89' to the list of known hosts.
To github.com:awsxuhi/test1.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@github.com:awsxuhi/test1.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

Follow below steps:

```shell
ssh-keygen
# Copy ~/.ssh/id_rsa.pub to github

git remote add origin git@github.com:awsxuhi/test1.git
cat .git/config

git clone git@github.com:awsxuhi/test1.git
git push -u origin master

```



## git branch

```shell
git remote -v
git branch
git branch new-feature # create a new branch named new feature
git branch

touch masteronly

git checkout new-feature #switch to branch "new-feature" from master
ls # You won't see the masteronly file
touch newfeatureonly
git add .
git commit -m "newfeatureonly"
git checkout master
ls # You won't see the newfeature only file.

#Now to merge branches
git diff new-feature # Your current branch is master
git merge new-feature # it's a new commit
ls # now you can see the newfeatureonly file.

```

What if there are conflicts between branches.If files changed in different branches, it won't be automatically merged correctly. You will find there are 2 modification in the same file, you need to modify the file manually and save it before you 

```shell
git add text.txt # manually modify it first.
git merge new-bugfix
git commit
git status
git push origin master #push to github
```



## git fetch

This section describes what to do when the local and remote versions are inconsistent. First, look at the remote side and do an extra commit, now the local is behind. If there are changes locally, then a merge operation is required.

```shell
#You update a file remotely in the git hub repo. Now in the local, you issue:
git remote -v
Mac➜  test1 git:(master) cat .git/HEAD # there is no FETCH_HEAD
ref: refs/heads/master
Mac➜  test1 git:(master) cat .git/refs/heads/master
2d90d8cdc60c7674f8f15eae3d8c32336f9e58f1   # this points to the latest commit number

git fetch origin master # Now a new file FETCH_HEAD was created.
Mac➜  test1 git:(master) cat .git/FETCH_HEAD
bd7684a2ff085d33280baaa7c0c20a22a37ae314		branch 'master' of github.com:awsxuhi/test1

git diff FETCH_HEAD

Mac➜  test1 git:(master) git merge FETCH_HEAD
Updating 2d90d8c..bd7684a
Fast-forward
 readme => fetch | 1 +
 1 file changed, 1 insertion(+)
 rename readme => fetch (55%)
Mac➜  test1 git:(master) ls
fetch  hehe   sshnew          #fetch is a new file in remote. now it's in local.

Mac➜  test1 git:(master) git push origin master
Everything up-to-date
```



## git pull

`Git pull` = `git fetch`+`git merge`



## git push/fetch to sync branch

This section describes how to synchronize local and remote branches.

Firstly, you create a local new brach named test by using `git checkout -b test` then:

```shell
git branch -a
git push origin test # sync to remote
git fetch # sync from remote
```



## git branch -D

To delete local branch test, run `git branch -D test`

To delete remote branch test, run `git push origin --delete test`

To rename a branch test2 to test3:

```shell
git checkout -b test2
git checkout master
git branch -m test2 test3  # -m=rename, it actually delete a branch before create a new one.
git push origin test3 # sync to remote
```



## git tag

```shell
git fetch -t # sync all tag from remote
git tag v1.1 # create a new tag point to current master
git tag # list all tag
git push --tag # push to remote
git tag -d v1.1 # delete local tag only
git fetch -t
git tag # v1.1 is still there, sync from remote
git push origin :refs/tags/v1.1 # delete remote tag 1.1 in github
```

In GitHub website, to create tag. click Code->release.







What's a GitHub fork. refer to https://www.cnblogs.com/lovecode521/p/5620724.html

