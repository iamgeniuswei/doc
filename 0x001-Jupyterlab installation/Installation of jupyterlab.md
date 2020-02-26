# jupyterlab 安装与配置

## 1. 准备docker镜像

拉取latest ubuntu镜像文件


```python
docker pull ubuntu
```

以拉取的latest ubuntu镜像文件运行一个容器，并将容器端口8888转发至主机端口8888


```python
docker run -it -p8888:8888 ubuntu:latest bash
```

在容器中安装文本编辑器nano


```python
apt install nano
```

替换国内源,将/etc/apt/sources.list替换为以下内容


```python
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

安装ca-certificates和apt-transport-https使https源可用


```python
apt install apt-transport-https & ca-certificates
```

安装python3与pip3


```python
apt install python3
apt install python3-pip
```

使用豆瓣pip源安装jupyterlab


```python
pip3 install jupyterlab -i https://pypi.douban.com/simple/
```

将容器保存为新的镜像


```python
docker commit 容器ID 镜像名称
```

## 2.运行jupyterlab

容器运行镜像，并将宿主机目录挂载至容器tmp目录，以便于保存笔记内容


```python
docker run -it -p8888:8888 -v /home/developer/Project/doc/:/tmp jupyterlab:latest bash
```

执行完毕后，返回类似以下结果：


```python
[I 01:44:16.597 LabApp] JupyterLab extension loaded from /usr/local/lib/python3.6/dist-packages/jupyterlab
[I 01:44:16.597 LabApp] JupyterLab application directory is /usr/local/share/jupyter/lab
[I 01:44:17.120 LabApp] Serving notebooks from local directory: /
[I 01:44:17.121 LabApp] The Jupyter Notebook is running at:
[I 01:44:17.121 LabApp] http://d208d72639ae:8888/?token=878196a6cbdce998e5af8689b34a21c2820c2069e411b582
[I 01:44:17.121 LabApp]  or http://127.0.0.1:8888/?token=878196a6cbdce998e5af8689b34a21c2820c2069e411b582
[I 01:44:17.121 LabApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[W 01:44:17.123 LabApp] No web browser found: could not locate runnable browser.
[C 01:44:17.123 LabApp] 
    
    To access the notebook, open this file in a browser:
        file:///root/.local/share/jupyter/runtime/nbserver-15-open.html
    Or copy and paste one of these URLs:
        http://d208d72639ae:8888/?token=878196a6cbdce998e5af8689b34a21c2820c2069e411b582
     or http://127.0.0.1:8888/?token=878196a6cbdce998e5af8689b34a21c2820c2069e411b582
[I 01:44:33.239 LabApp] 302 GET /?token=878196a6cbdce998e5af8689b34a21c2820c2069e411b582 (172.17.0.1) 0.81ms
```

根据返回的链接http://127.0.0.1:8888/?token=878196a6cbdce998e5af8689b34a21c2820c2069e411b582， 在宿主机中访问jupyterlab

## 3.使用github同步数据

在容器中安装jupyter-git，首先安装依赖库nodejs与npm

apt install nodejs


```python
apt install npm
```

安装jupyterlab-git


```python
pip3 install --upgrade jupyterlab-git -i https://pypi.doubanio.com/simple
```

将jupyterlab-git扩展编译进jupyterlab


```python
!jupyter lab build
```

重新启动jupyter lab


```python
jupyter lab --allow-root --ip=0.0.0.0 --port=8888
```

在jupyterlab中打开terminal，配置git设置和秘钥


```python
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```


```python
ssh-keygen -t rsa -C "you@example.com"
```

将生成的.id_rsa.pub中内容拷贝至github中SSH Keys，并测试密钥是否生效：


```python
!ssh git@github.com
```

    PTY allocation request failed on channel 0
    Hi iamgeniuswei! You've successfully authenticated, but GitHub does not provide shell access.
    Connection to github.com closed.


配置成功后，即可按照常规github流程将notebook同步至github中。
