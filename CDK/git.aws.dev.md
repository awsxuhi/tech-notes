How to git clone from gitlab.aws.dev



You should follow the following steps:



```shell
cat ./ssh/id_rsa.pub

# Copy the entire output, go to gitlab->perference->ssh, click add a ssh key and paste what you've copied

eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa

ssh-add -l
# 3072 SHA256:qc9OzvOCR+hrr0i1qljNP5CGZSdcJ9JhZ47Zg8CsvNU xuhi@f84d89960716.ant.amazon.com (RSA)

ssh -T git@ssh.gitlab.aws.dev
# git@ssh.gitlab.aws.dev: Permission denied (publickey).

# In your terminal run: mwinit
> mwinit
PIN for xuhi:
Press the button on your U2F token...
Successfully authenticated using u2f, session cookie saved in /Users/xuhi/.midway/cookie
Successfully signed SSH public key /Users/xuhi/.ssh/id_rsa.pub. The SSH certificate was saved in /Users/xuhi/.ssh/id_rsa-cert.pub

# Now you can clone repo successfully
git clone git@ssh.gitlab.aws.dev:xuhi/my-test-project.git
```

