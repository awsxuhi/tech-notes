

### Step 1: Initiate a CDK project

Cdk init

```shell
mkdir github-actions-cicd
cd github-actions-cicd
cdk init app --language typescript

midir .git/workflows
code .

# upgrade cdk
sudo npm install -g aws-cdk
cdk --version

npm install
cdk deploy --verbose --all --require-approval never

# After successfully tested the deployment from local mac, we are trying to use github CICD
git log
rm -rf .git
git add .
git commit -m "Initial commit"

# Create a new repo named github-actions-cicd in github
# Now push local branch to remote
git remote add origin git@github.com:awsxuhi/github-actions-cicd.git
# git init will only create master branch, so we need to renamed it to main
git branch -M main  
git push -u origin main

```



```shell
cd .git/hooks
vi pre-commit

# past below code to the file
# 这个脚本读取 .env 文件中的 VERSION 值，递增版本号的最后一部分，然后用新的版本号替换旧的版本号，并将更改添加到 Git。
#!/bin/bash

VERSION=$(sed -n 's/VERSION="\([^"]*\)"/\1/p' .env)

if [[ -n "$VERSION" ]]; then
    # Increment version.
    IFS='.' read -ra ADDR <<< "$VERSION"
    last_index=$((${#ADDR[@]}-1))
    ADDR[last_index]=$((${ADDR[last_index]} + 1))
    NEW_VERSION=$(IFS=.; echo "${ADDR[*]}")

    # Replace old version with new version in .env file
    perl -pi -e "s/$VERSION/$NEW_VERSION/g" .env
    echo "New version: $NEW_VERSION"
    git add .env
fi

# after save the file
chmod 755 pre-commit
```



in MacOs

```shell
vi /Users/<Your Username>/Library/Application Support/Code/User/settings.json
```

And add 

```json
"files.exclude": {
    "**/.git": false
}
```

After that, the file looks like:

```json
  1 {
  2   "[javascript]": {
  3     "editor.defaultFormatter": "esbenp.prettier-vscode",
  4     "editor.tabSize": 2
  5   },
  6   "[json]": {
  7     "editor.defaultFormatter": "esbenp.prettier-vscode"
  8   },
  9   "[python]": {
 10     "editor.formatOnType": true,
 11     "editor.tabSize": 4
 12   },
 13   "[typescript]": {
 14     "editor.defaultFormatter": "esbenp.prettier-vscode",
 15     "editor.tabSize": 2
 16   },
 17   "[typescriptreact]": {
 18     "editor.defaultFormatter": "esbenp.prettier-vscode",
 19     "editor.tabSize": 2
 20   },
 21   "amazonQ.shareContentWithAWS": false,
 22   "aws.codeWhisperer.shareCodeWhispererContentWithAWS": false,
 23   "aws.suppressPrompts": {
 24     "codeWhispererNewWelcomeMessage": true
 25   },
 26   "editor.accessibilitySupport": "off",
 27   "editor.codeActionsOnSave": {},
 28   "editor.defaultFormatter": "esbenp.prettier-vscode",
 29   "editor.formatOnPaste": true,
 30   "editor.formatOnSave": true,
 31   "editor.minimap.enabled": false,
 32   "editor.tabSize": 2,
 33   "editor.wordWrap": "bounded",
 34   "editor.wordWrapColumn": 300,
 35   "files.autoSave": "onFocusChange",
 36   "files.exclude": {
 37     "**/.git": false
 38   },
 39   "git.ignoreMissingGitWarning": true,
 40   "git.openRepositoryInParentFolders": "never",
 41   "git.path": "/usr/bin/git",
 42   "github.gitProtocol": "ssh",
 43   "html.format.enable": false,
 44   "javascript.updateImportsOnFileMove.enabled": "always",
 45   "liveServer.settings.donotShowInfoMsg": true,
 46   "prettier.jsxSingleQuote": true,
 47   "prettier.singleQuote": true,
 48   "prettier.tabWidth": 4,
 49   "python.analysis.autoSearchPaths": false,
 50   "python.defaultInterpreterPath": "/Users/xuhi/.local/share/virtualenvs/streamlit-CQBGnwtA/bin/python",
 51   "python.venvPath": "~/.pyenv,  ~/.local/share/virtualenvs/",
 52   "terminal.integrated.env.osx": {
 53     "Q_NEW_SESSION": "1"
 54   },
 55   "vsicons.dontShowNewVersionMessage": true,
 56   "window.zoomLevel": 1,
 57   "workbench.colorCustomizations": {
 58     "tab.activeBackground": "#035c0f"
 59   },
 60   "workbench.iconTheme": "material-icon-theme"
 61 }
```



`.git/hooks` 目录中的钩子脚本文件不会被 Git 跟踪和推送到远程仓库（例如 GitHub）。这是因为 Git 钩子脚本是特定于本地仓库的设置，通常不会包括在版本控制中。
