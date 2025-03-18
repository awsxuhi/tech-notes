Lambda Container

#### Dockfile (how to write dockfile)

一个典型的dockfile如下所示，主要的注意事项列在里面。

```dockerfile
# 当我们使用Mac笔记本，并通过Docker Desktop进行容器打包的时候，如果Mac笔记本是M1芯片，这属于ARM架构。此时如果最终的Lambda是
# 运行在X86的架构的服务器上，就需要使用“--platform=linux/amd64”
# 这里的public.ecr.aws/lambda/python:3.10是AWS提供的基于Amazon Linux 2.0的基础镜像
# 我在打包streamlit的时候使用的是“python:3.8-slim”这个镜像，因为AppRunner目前只支持Python 3.7和3.8，可惜不成功
# Use a base image with Python 3.10
FROM --platform=linux/amd64 public.ecr.aws/lambda/python:3.10
# FROM --platform=linux/amd64 python:3.8-slim

# Check the OS of the base image to confirm it's Amazon Linux 2
RUN cat /etc/os-release

# Install necessary tools for downloading and unpacking only if they don't exist
# 下面这几个命令是用来保证基础镜像中包含wget, tar, gzip等基础工具。在Amazon Linux中已经包含了wget和tar，但是在
# 使用tar zxvf *.tar.gz的时候报告错误“缺少gzip“，所以还是需要安装gzip
RUN command -v wget >/dev/null 2>&1 || yum install -y wget
RUN command -v tar >/dev/null 2>&1 || yum install -y tar
RUN command -v gzip >/dev/null 2>&1 || yum install -y gzip

# Download and unpack LibreOffice，这个包用来处理Microsoft Office相关的文档，例如可以读取*.docx文件。
RUN wget https://download.documentfoundation.org/libreoffice/stable/7.6.0/rpm/x86_64/LibreOffice_7.6.0_Linux_x86-64_rpm.tar.gz && \
    tar zxvf LibreOffice_7.6.0_Linux_x86-64_rpm.tar.gz

# Install LibreOffice and other necessary packages
RUN yum install /var/task/LibreOffice_7.6.0.3_Linux_x86-64_rpm/RPMS/*.rpm -y
RUN yum install java-1.8.0-openjdk -y

# Create symlink for soffice
RUN ln -s /opt/libreoffice7.6/program/soffice /usr/bin/soffice

# Copy nltk_data directory
# 这里注意当copy一个目录到另一个目录，需要在结尾加上`/`，否则容易被识别为文件，而不是目录
COPY nltk_data/ ${LAMBDA_TASK_ROOT}/nltk_data/

# To check if the directory was successfully copied
RUN ls -la ${LAMBDA_TASK_ROOT}/nltk_data

# Copy requirements.txt
COPY requirements.txt ${LAMBDA_TASK_ROOT}

# Install the specified packages
# Python 3.7-3.10: Runtime-included libraries are installed in the /var/runtime directory. Pip-installed libraries are installed in the /var/lang/lib/python3.x/site-packages directory. The /var/runtime directory has precedence over /var/lang/lib/python3.x/site-packages in the search path.
RUN pip install -r requirements.txt --target "${LAMBDA_TASK_ROOT}"

# Copy function code, leverage the cache during re-build, so put this line below.
# 这里首先是注意目录的后缀要有/，但是把库文件放在csdc_lib目录下后，lambda执行的时候会找不到这些库文件，因为这个目录不在
# 系统路径中，需要在lambda中使用下面的代码来保证这个目录在路径中。
# import sys
# sys.path.append("/var/task/csdc_lib")
# from csdc_lib.llm_wrapper import ChatCSDC
# from csdc_lib.sagemaker_utils import create_sagemaker_embeddings_from_js_model
# 如果不想这么处理，也可以使用下面的命令把csdc_lib的文件全部拷贝到lambda运行环境的根目录中，这样能找到，但是不利于本地调试。
# COPY csdc_lib/* ${LAMBDA_TASK_ROOT}/

COPY csdc_lib/ ${LAMBDA_TASK_ROOT}/csdc_lib/
COPY main.py ${LAMBDA_TASK_ROOT}

# Set the CMD to your handler (could also be done as a parameter override outside of the Dockerfile)
CMD [ "main.lambda_handler" ]

# https://docs.aws.amazon.com/lambda/latest/dg/python-image.html
```



