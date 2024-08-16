## Install pipenv in the terminal of SageMaker Studio

Open SageMaker Studio, click Launch, then select Terminal. 

Now you are in an Amazon Linux environment.

#### Step 1: install pipenv

```shell
pip install pipenv
```



#### Step 2.1: install pyenv (failed)

```shell
curl https://pyenv.run | bash
```

add below to ~/.bash_profile which is not existed now.

```shell
export PYENV_ROOT="$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

Execute the following command

```shell
source ~/.bash_profile
```

Install gcc and related development tools (otherwise "pyenv install 3.10" will fail.)

```shell
sudo yum groupinstall "Development Tools"
```

Install required lib

```
sudo yum install bzip2-devel ncurses-devel libffi-devel openssl-devel readline-devel

# otherwise you will get the following warning when you execute "pyenv install 3.10"
ModuleNotFoundError: No module named '_bz2'
WARNING: The Python bz2 extension was not compiled. Missing the bzip2 lib?
ModuleNotFoundError: No module named '_curses'
WARNING: The Python curses extension was not compiled. Missing the ncurses lib?
ModuleNotFoundError: No module named '_ctypes'
WARNING: The Python ctypes extension was not compiled. Missing the libffi lib?
ModuleNotFoundError: No module named 'readline'
WARNING: The Python readline extension was not compiled. Missing the GNU readline lib?
ModuleNotFoundError: No module named '_ssl'
ERROR: The Python ssl extension was not compiled. Missing the OpenSSL lib?
```



Install pyenv, without the patch number, it will automatically install the latest version (e.g., 3.10.13)

```shell
pyenv install 3.10
```

Unfortunately, even we've installed lib, we still got warning when issuing pyenv install 3.10. It looks like pyenv conflicts with Sagemaker. Sagemaker is using conda. Let's start to use conda to manage environment.



#### Step 2.2: use Conda to create venv

Issue the command at any directory which doesn't matter. 

```shell
conda create --name myenv python=3.10
```

Activate the myenv

```shell
conda activate myenv
```

Cd to the working directory

```shell
cd qabot/lib/streamlit

pipenv install
# Now it will install all libs showed in the Pipfile

nohup pipenv run streamlit run webapp.py &
```



#### Conclusion: Run streamlit in SageMaker Studio Terminal

Issue the following command step by step:

```shell
# Clone the repo to the SageMaker Studio
git clone git@github.com:awsxuhi/qa-bot-streamlit.git

# Install pipenv if you've not installed it
pip install pipenv

# Create an virtual env named myenv
conda create --name myenv python=3.10
conda activate myenv

cd qa-bot-streamlit
# Install libs showed in Pipfile
pipenv install
nohup pipenv run streamlit run webapp.py &

# Check if streamlit webserver is running
ps aux | grep streamlit
```

