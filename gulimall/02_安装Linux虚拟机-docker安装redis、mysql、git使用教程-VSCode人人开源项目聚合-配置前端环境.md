# 02_安装Linux虚拟机-docker安装redis、mysql、git使用教程-VSCode人人开源项目聚合-配置前端环境

# 一、安装Linux虚拟机

## 1.1 安装Linux虚拟机

下载并安装VirtualBox虚拟机，要开启CPU虚拟化

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%201.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%201.png)

## 1.2 下载并安装VirtualBox

虚拟机Oracle VM VirtualBox官网地址：[https://www.virtualbox.org/](https://www.virtualbox.org/)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%202.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%202.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%203.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%203.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%204.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%204.png)

只改盘符，选择D盘，其他默认点击下一步即可

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%205.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%205.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%206.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%206.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%207.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%207.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%208.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%208.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%209.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%209.png)

## 1.3 下载并安装Vagrant

Vagrant官方镜像仓库：[https://app.vagrantup.com/boxes/search](https://app.vagrantup.com/boxes/search)

Vagrant下载：[https://www.vagrantup.com/downloads](https://www.vagrantup.com/downloads)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2010.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2010.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2011.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2011.png)

然后点击Next安装稍等片刻即可。重启电脑

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2012.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2012.png)

> 1、打开cmd窗口输入以下命令：`vagrant`查看我们是否安装成功Vagrant
> 

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2013.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2013.png)

> 2、打开window cmd窗口，运行`vagrant init centos/7` ，即可初始化一个centos7系统
> 

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2014.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2014.png)

[https://www.notion.so](https://www.notion.so)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2015.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2015.png)

> 3、在cmd命令窗口运行`vagrant up` 即可启动虚拟机。系统root用户的密码是vagrant
> 

首先要确保上一步操作生成了Vagrantfile文件

稍微有点慢，耐心等待，此时打开我们的虚拟机Oracle VM VirtualBox会发现多了一个虚拟机

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2016.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2016.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2017.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2017.png)

> 4、 `vagrant ssh`：自动使用vagrant 用户连接虚拟机。
> 

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2018.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2018.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2019.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2019.png)

`[vagrant@localhost ~]$` 查看当前是哪个用户

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2020.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2020.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2021.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2021.png)

退出连接：`[vagrant@localhost ~]$` `exit;`

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2022.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2022.png)

> 4.1、vagrant upload source [destination] [name|id]：上传文件
> 

### 虚拟网络设置

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2023.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2023.png)

> 5、默认虚拟机的ip地址不是固定ip,开发不方便
> 

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2024.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2024.png)

> 5.1 修改Vagrantfile文件：文件地址：C:\Users\lenovo\Vagrantfile 【不同电脑位置略有不同，以自己电脑cmd窗口提示为准】
> 

首先需要通过cmd窗口`ipconfig`命令查看IP地址

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2025.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2025.png)

我们修改修改Vagrantfile文件文件：`config.vm.network "private_network", ip: "192.168.56.10"` ip地址，前三组必须与VirtualBox Host- Only Ne twork: 中的IPv4地址的前三组【`192.168.56.一般改成10就可以`】保持一致，后一组可自行修改。本文案例修改`192.168.56.10` 。

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2026.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2026.png)

修改之后保存文件，然后重启虚拟机。此时必须通过`vagrant reload` 命令修改的ip才可以生效。

### 重启虚拟机命令：`vagrant reload`

### 退出命令：`exit;`

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2027.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2027.png)

### 连接虚拟机命令：`vagrant ssh`

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2028.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2028.png)

### 虚拟机查看IP地址：`[vagrant@localhost ~]$ ip addr`

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2029.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2029.png)

各自ping通进行测试：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2030.png)

![02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%2031.png](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2031.png)

说明我们的ip地址就算是设置好了，网络环境正常。

# 二、安装docker

## docker Hub：docker安装市场

[Docker Hub Container Image Library | App Containerization](https://hub.docker.com/)

Docker Hub市场

## docker 安装手册

[Install Docker Engine on CentOS](https://docs.docker.com/engine/install/centos/)

安装教程：

![image-20220530161233628](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/image-20220530161233628.png)



## 2.1 卸载系统之前的docker

[https://www.notion.so](https://www.notion.so)

```bash
 sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2033.png)

## 2.2 设置repository

在首次在新的主机上安装 Docker 引擎之前，您需要设置 Docker 存储库。之后，您可以从存储库安装和更新 Docker。

安装封装（提供实用程序）并设置稳定的存储库。yum-utilsyum-config-manager

第一步：

```bash
 sudo yum install -y yum-utils
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2034.png)

第二步：

```bash
 sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2035.png)

## 2.3 安装Docker Engine

安装最新版本的 Docker 引擎和容器

```bash
sudo yum install docker-ce docker-ce-cli containerd.io
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2036.png)

### 2.3.1 启动docker

```bash
sudo systemctl start docker
```

> 查看docker版本：`[vagrant@localhost ~]$ docker -v`
> 

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2037.png)

### 2.3.2 检测docker中下载了哪些镜像命令：`$ sudo docker images`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2038.png)

### 2.3.4 给docker设置开机自启动

```bash
sudo systemctl enable docker
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2039.png)

## 2.4 环境配置docker阿里云镜像加速



视频教程

[阿里云登录 - 欢迎登录阿里云，安全稳定的云计算服务平台](https://cr.console.aliyun.com/cn-qingdao/instances/mirrors)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2040.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2041.png)

分批执行以下命令：

```bash
sudo mkdir -p /etc/docker

===================================================

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://1f0809nt.mirror.aliyuncs.com"]
}
EOF

===================================================

sudo systemctl daemon-reload      #重启docker后台线程

===================================================

sudo systemctl restart docker     #重启docker服务
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2042.png)

# 三、docker安装MySQL



## 1、首先去docker Hub去搜索并下载MySQL镜像文件`sudo docker pull mysql:5.7`

[Docker Hub Container Image Library | App Containerization](https://hub.docker.com/)

启动虚拟机并连接之后执行以下命令：安装MySQL

`[vagrant@localhost ~]$ sudo docker pull mysql:5.7`

### 1.1 检查安装的镜像文件：`[vagrant@localhost ~]$ sudo docker images`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2043.png)

### 1.2 切换root用户命令：`su root` 密码为：vagrant ，这样就可以不写sudo了

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2044.png)

### 1.3 查看当前用户的命令：`whoami`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2045.png)

## 2、创建实例并启动MySQL

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2046.png)

```bash
sudo docker pull mysql:5.7

# --name指定容器名字 -v目录挂载 -p指定端口映射  -e设置mysql参数 -d后台运行

————————————————
版权声明：本文为CSDN博主「hancoder」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/hancoder/article/details/106922139

[备注]切换root用户命令：su root 密码为：vagrant ，这样就可以不写sudo了
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2047.png)

### docker容器文件挂载与端口映射

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2048.png)

### 2.1 查看docker中正在运行的容器命令：`[root@localhost vagrant]# docker ps`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2049.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2050.png)

验证MySQL容器确实一个完整的Linux

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2051.png)

### 查看所有的容器（没有运行的也可以查看）： `docker ps -a`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2052.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2053.png)

### 第二次启动MySQL命令：docker start mysql

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2054.png)

- 服务器重启后，docker安装的mysql怎么重启
  
    列出docker中运行的容器命令：`docker ps -a`
    
    docker启动MySQL命令：`[root@localhost vagrant]# docker restart 0923afbb97fc`
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2055.png)
    

### 查看MySQL安装的位置命令：`whereis mysql`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2056.png)

### 2.2 navicat连接MySQL服务

**mysql密码：root**

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2057.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2058.png)

## MySQL配置

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2059.png)

```bash
docker exec -it mysql bin/bash
exit;

-------------------------------------------------------------------------

因为有目录映射，所以我们可以直接在镜像外执行
vi /mydata/mysql/conf/my.conf 

[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
init_connect='SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake
skip-name-resolve

保存(注意评论区该配置不对，不是collection而是collation)

docker restart mysql   #重启MySQL容器
————————————————
版权声明：本文为CSDN博主「hancoder」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/hancoder/article/details/106922139
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2060.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2061.png)

### 重启MySQL容器命令：`docker restart mysql`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2062.png)

### MySQL日志文件地址：/mydata/mysql/log

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2063.png)

# 四、docker安装redis

## 4.1 下载reids镜像文件命令：`docker pull redis`

```bash
C:\Users\lenovo>vagrant ssh
Last login: Thu Aug 12 09:16:01 2021 from 10.0.2.2
Last login: Thu Aug 12 09:16:01 2021 from 10.0.2.2
[vagrant@localhost ~]$ su root     #切换到root用户
Password:vagrant
[root@localhost vagrant]# docker pull redis  #没有指定版本就是下载最新的镜像
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2064.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2065.png)

## 4.2 创建实例并启动（redis配置文件）

如果直接挂载的话docker会以为挂载的是一个目录，所以我们先创建一个文件然后再挂载，在虚拟机中。

```bash
# 在虚拟机中
mkdir -p /mydata/redis/conf

touch /mydata/redis/conf/redis.conf
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2066.png)

```bash
docker run -p 6379:6379 --name redis \
-v /mydata/redis/data:/data \
-v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
-d redis redis-server /etc/redis/redis.conf
————————————————
版权声明：本文为CSDN博主「hancoder」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/hancoder/article/details/106922139

# 直接进去redis客户端。
docker exec -it redis redis-cli
```

### 4.3.1 redis配置文件地址： /mydata/redis/conf/redis.conf

### 4.3.2 直接进去redis-cli客户端命令：`docker exec -it redis redis-cli`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2067.png)

### 4.3.3 重启动reids命令：`docker restart redis`   # 重启reids

```bash
127.0.0.1:6379> exit        #退出reids
[root@localhost conf]# docker ps    # 查看镜像文件
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                                                  NAMES
597b1daa162c   redis       "docker-entrypoint.s…"   11 minutes ago   Up 11 minutes   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp              redis
0923afbb97fc   mysql:5.7   "docker-entrypoint.s…"   7 hours ago      Up 2 hours      0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql
[root@localhost conf]# docker restart redis   # 重启reids
redis
[root@localhost conf]# docker exec -it redis redis-cli  #进入redis-cli
127.0.0.1:6379> get a
```

默认是不持久化的。在配置文件中输入`appendonly yes`，就可以aof持久化了。修改完`docker restart redis，docker -it redis redis-cli` (最新版的redis貌似支持持久化)

```bash
vim /mydata/redis/conf/redis.conf

# 插入下面内容
appendonly yes

保存

docker restart redis  #重启reids
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2068.png)

关于redis.conf的更多配置可以参考官网：

[Redis configuration - Redis](https://redis.io/topics/config)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2069.png)

```bash
[root@localhost conf]# vi redis.conf
[root@localhost conf]# docker restart redis    --重启redis命令
redis
[root@localhost conf]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS         PORTS                                                  NAMES
597b1daa162c   redis       "docker-entrypoint.s…"   19 minutes ago   Up 7 seconds   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp              redis
0923afbb97fc   mysql:5.7   "docker-entrypoint.s…"   7 hours ago      Up 2 hours     0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql
[root@localhost conf]# docker exec -it redis redis-cli
127.0.0.1:6379> set aa bb
OK
127.0.0.1:6379> get aa
"bb"
127.0.0.1:6379> exit   --退出redis
[root@localhost conf]# docker restart redis     --重启redis命令
redis
[root@localhost conf]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS         PORTS                                                  NAMES
597b1daa162c   redis       "docker-entrypoint.s…"   22 minutes ago   Up 6 seconds   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp              redis
0923afbb97fc   mysql:5.7   "docker-entrypoint.s…"   7 hours ago      Up 2 hours     0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   mysql
[root@localhost conf]# docker exec -it redis redis-cli   --重要：进入redis
127.0.0.1:6379> get aa
"bb"
127.0.0.1:6379>
```

使用redis客户端连接

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2070.png)

## 4.3 使用redis镜像执行redis-cli命令

```bash
[root@localhost vagrant]# docker restart redis     #重启redis
redis
[root@localhost vagrant]# docker exec -it redis redis-cli  #使用redis镜像执行redis-cli命令
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2071.png)

# 五、开发环境统一：环境开发工具&环境安装配置

## 5.1 Maven配置

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2072.png)

在settings中配置阿里云镜像，配置jdk1.8。这个基本都配置过，不贴了

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2073.png)

## 5.2 IDEA&VsCode

IDEA安装插件lombok，mybatisX。IDEA设置里配置好maven

### 5.2.1 IDEA安装的插件：MyBatisx、Lombok

### 5.2.2 VsCode的安装与配置

下载vsCode用于前端管理系统。在vsCode里安装以下插件：

- Auto Close Tag
- Auto Rename Tag
- Chinese
- ESlint
- HTML CSS Support
- HTML Snippets
- JavaScript ES6
- Live Server
- open in brower
- Vetur

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2074.png)

## 5.3 安装配置git：另外推荐sourcetree

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2075.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2076.png)

下载git客户端，右键桌面Git GUI/bash Here。去bash，

```bash
# 配置用户名
git config --global user.name "username"  //(名字，随意写)

# 配置邮箱
git config --global user.email "55333@qq.com" // 注册账号时使用的邮箱

# 配置ssh免密登录
ssh-keygen -t rsa -C "55333@qq.com"
三次回车后生成了密钥：公钥私钥
cat ~/.ssh/id_rsa.pub

也可以查看密钥
浏览器登录码云后，个人头像上点设置--ssh公钥---随便填个标题---复制
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC6MWhGXSKdRxr1mGPZysDrcwABMTrxc8Va2IWZyIMMRHH9Qn/wy3PN2I9144UUqg65W0CDE/thxbOdn78MygFFsIG4j0wdT9sdjmSfzQikLHFsJ02yr58V6J2zwXcW9AhIlaGr+XIlGKDUy5mXb4OF+6UMXM6HKF7rY9FYh9wL6bun9f1jV4Ydlxftb/xtV8oQXXNJbI6OoqkogPKBYcNdWzMbjJdmbq2bSQugGaPVnHEqAD74Qgkw1G7SIDTXnY55gBlFPVzjLWUu74OWFCx4pFHH6LRZOCLlMaJ9haTwT2DB/sFzOG/Js+cEExx/arJ2rvvdmTMwlv/T+6xhrMS3 553736044@qq.com

# 测试
ssh -T git@gitee.com
测试成功，就可以无密给码云推送仓库了
```

## 5.4 逆向工程使用

### 5.4. 1、导入项目逆向工程

### 5.4.2、下载人人开源后台管理系统脚手架工程

(1) 导入工程，创建数据库

(2)修改工程shiro依赖为SpringSecurity

(3)删除部分暂时不需要的业务

### 5.4.3、下载人人开源后台管理系统vue端脚手架工程

(1) vscode导入前端项目

(2) 前后端联调测试基本功能

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2077.png)

# 六、创建项目微服务：项目的聚合

## 现有项目

项目刚出来的时候老师没把代码放出来，但现在好像把完整版的代码放出来了，所以你可以运行老师给的项目试一下。

但是老师的项目nacos等信息可能没有，还需要你自己搭建。一个好的方式是编译nacos的源码项目到IDEA中。而这种解决方案网上开源的同学们都是这么做的。

除此之外，基本老师给的项目里都全了，你要做的就是改改里面的配置信息等。作为参数，我提供一个别人的链接吧：[https://github.com/1046762075/mall](https://github.com/1046762075/mall) 把他拉取到你的本地或者克隆到你的码云上再拉取都可以，但是把项目运行起来可不是简单的事，有很多东西需要自己配置

## 自己从0到1构建一个项目步骤

## 6.1 从gitee初始化一个项目

在码云新建仓库，仓库名gulimall，选择语言java，在.gitignore选中maven（就会忽略掉maven一些个人无需上传的配置文件），许可证选Apache-2.0，开发模型选生成/开发模型，开发时在dev分支，发布时在master分支，创建。

————————————————

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2078.png)

在IDEA中New–Project from version control–git–复制刚才项目的地址：[https://gitee.com/SuperVITA/gulimall.git](https://gitee.com/SuperVITA/gulimall.git)

IDEA然后New Module–Spring Initializer–com.atguigu.gulimall ， Artifact填 gulimall-product。

从码云拉取项目到idea本地，新建Module

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2079.png)

Next—选择web（web开发），springcloud routing里选中openFeign（rpc调用）。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2080.png)

## 6.2 创建各个微服务项目

### 6.2.1 创建各个微服务项目共同点：

- 导入web和openFeign：选择web，springcloud routing里选中openFeign（rpc调用）
- group：com.atguigu.gulimall
- Artifact：gulimall-XXX
- 每一个服务，[包名com.atguigu.gulimall.XXX](http://xn--com-k82e60c.atguigu.gulimall.xxx/){product/order/ware/coupon/member}
- 模块名：gulimall-XXX

### 6.2.2 依次创建出以下服务

- 商品服务product：gulimall-product
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2081.png)
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2082.png)
    
- 存储服务ware：gulimall-ware
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2083.png)
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2084.png)
    
- 订单服务order：gulimall-order
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2085.png)
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2086.png)
    
- 优惠券服务coupon：gulimall-coupon
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2087.png)
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2088.png)
    
- 会员服务member：gulimall-member
  
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2089.png)
    
    ![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2090.png)
    

## 6.3 将gulimall项目设置成总项目：聚合那几个小项目

然后右下角显示了springboot的service选项，选择他

从某个项目粘贴个pom.xml粘贴到gulimall跟项目目录，修改pom文件为：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.atguigu.gulimall</groupId>
    <artifactId>gulimall</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>gulimall</name>
    <description>聚合服务</description>

    <packaging>pom</packaging>

    <modules>
        <module>gulimall-coupon</module>
        <module>gulimall-member</module>
        <module>gulimall-order</module>
        <module>gulimall-product</module>
        <module>gulimall-ware</module>
    </modules>
</project>
```

修改好pom文件后在idea中进行如下配置：

在maven窗口刷新，并点击+号，找到刚才的pom.xml添加进来，发现多了个root。这样比如运行root的clean命令，其他项目也一起clean了。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2091.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2092.png)

## idea中忽略提交git（码云）不必要的文件设置：

修改总项目的`.gitignore`，把小项目里的垃圾文件在提交的时候忽略掉，[比如HELP.md](http://xn--help-f96g315g.md/)。。。

```xml
**/mvnw
**/mvnw.cmd
**/.mvn
**/target/
.idea
**/.gitignore
```

在version control/local Changes，点击刷新看Unversioned Files，可以看到变化。

全选最后剩下21个文件，选择右键、Add to VCS。

在IDEA中安装插件：gitee，重启IDEA。

在D额fault changelist右键点击commit，去掉右面的勾选P`erform code analysis`、`CHECK TODO`，然后点击`COMMIT`，有个下拉列表，点击`commit and push`才会提交到云端。此时就可以在浏览器中看到了。

commit只是保存更新到本地，push才是提交到gitee

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2093.png)

## 环境_项目结构创建&提交到码云，idea中提交代码到（git）码云仓库：谷粒商城项目初始结构创建

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2094.png)

## 环境-MySQL数据库初始化：MySQL数据库设计

## powerdesigner：MySQL数据库设计软件

[](http://forspeed.onlinedown.net/down/powerdesigner1029.zip)

课件提供了sql，但是里面只有表，没有数据，所以建议不要用课件里的。

另外视频评论区的sql文件有问题，后面和sql查询java-entity类对应不上

我自己修改过的sql上传到了：[https://github.com/FermHan/gulimall](https://github.com/FermHan/gulimall.git)

使用的时候注意下数据库名和IDEA中设置是否匹配。

如何改：加上类似下面的语句

```xml
CREATE DATABASE /*!32312 IF NOT EXISTS*/`guli_pms` /*!40100 DEFAULT CHARACTER SET utf8mb4 */;

-- 根据需要改数据库名即可，表名无需改动
USE `gulimall_pms`;
-- 也集合P71的内容进行了修改
```

因为已经有人贡献sql文件了，所以我们不理会下面引用部分的内容了。

安装powerDesigner软件。[http://forspeed.onlinedown.net/down/powerdesigner1029.zip](http://forspeed.onlinedown.net/down/powerdesigner1029.zip)

其他软件：
[https://www.lanzous.com/b015ag33e](https://www.lanzous.com/b015ag33e) ,密码:2wre

所有的数据库数据再复杂也不建立外键，因为在电商系统里，数据量大，做外键关联很耗性能。

name是给我们看的，code才是数据库里真正的信息。

选择primary和identity作为主键。然后点preview就可以看到生成这张表的语句。

点击菜单栏database–generate database—点击确定

### docker中设置redis自动启动和MySQL自动启动命令：只要虚拟机重启我们的MySQL和redis都会自动启动

```bash
sudo docker ps
sudo docker ps -a
# 这两个命令的差别就是后者会显示  【已创建但没有启动的容器】

# 我们接下来设置我们要用的容器每次都是自动启动
sudo docker update redis --restart=always
sudo docker update mysql --restart=always
# 如果不配置上面的内容的话，我们也可以选择手动启动
sudo docker start mysql
sudo docker start redis
# 如果要进入已启动的容器
sudo docker exec -it mysql /bin/bash
# /bin/bash就是进入一般的命令行，如果改成redis就是进入了redis
```

## 在navicat中创建对应的数据库：gulimall_oms、gulimall_pms、gulimall_sms、gulimall_ums、gulimall_wms

然后接着去navicat执行我们的操作，在左侧谷粒商城_192.......上右键建立数据库：字符集选utf8mb4，他能兼容utf8且能解决一些乱码的问题。分别建立了下面数据库（根据自己情况来）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2095.png)

```bash
gulimall-oms
gulimall-pms
gulimall-sms
gulimall-ums
gulimall-wms
```

然后打开对应的sql在对应的数据库中执行。依次执行。(注意sql文件里没有建库语句)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2096.png)

## 码云搜索：人人开源

[人人开源](https://gitee.com/renrenio)

在码云上搜索人人开源，我们使用`renren-fast`（后端）、`renren-fast-vue`（前端）项目。

```bash
git clone https://gitee.com/renrenio/renren-fast.git

git clone https://gitee.com/renrenio/renren-fast-vue.git
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2097.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2098.png)

下载到了桌面，我们把renren-fast移动到我们的项目文件夹（删掉.git文件），而renren-vue是用VSCode打开的（后面再弄）

在IDEA项目里的pom.xml添加一个renrnen-fast

```xml
<modules>
    <module>gulimall-coupon</module>
    <module>gulimall-member</module>
    <module>gulimall-order</module>
    <module>gulimall-product</module>
    <module>gulimall-ware</module>

    <module>renren-fast</module>
</modules>
```

然后打开renren-fast/db/mysql.sql，复制全部，在sqlyog中创建库guli-admin，粘贴刚才的内容执行。【注意给renren-fast这个项目导入JDK】

然后修改项目里renren-fast中的application.yml【默认选择开发环境为：dev】，然后修改`application-dev.yml`中的数库库的url，通常把localhost修改为192.168.56.10即可。然后该对后面那个数据库。

```bash
url: jdbc:mysql://192.168.56.10:3306/guli_admin?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
username: root
password: root

#视频版：gulimall_admin数据库名   建议用这个
url: jdbc:mysql://192.168.56.10:3306/gulimall_admin?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
username: root
password: root
```

然后运行该java项目下的`RenrenApplication`

浏览器输入`[http://localhost:8080/renren-fast/](http://localhost:8080/renren-fast/)` 得到`{“msg”:“invalid token”,“code”:401}`就代表无误。

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%2099.png)

## 用VSCode打开：人人开源项目clone的项目renren-fast-vue

用VSCode打开renren-fast-vue（如果自己搭建的话），如果是运行完整的代码，可以去课件里找gulimall-admin-vue-app

## 下载并安装node：(注意添加path环境变量,后期要用到)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%20100.png)

### 查看node的版本信息：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%20101.png)

http://nodejs.cn/download/ 选择windows下载。下载完安装。

可以去这里找到v12的版本。（不要用12.0，可以用12.1）https://npm.taobao.org/mirrors/node/

[Node.js Mirror](https://npm.taobao.org/mirrors/node/)

视频教程中的node版本：V10.16.3

[](https://cdn.npm.taobao.org/dist/node/v10.16.3/node-v10.16.3-x64.msi)

`NPM`是随同`NodeJS`一起安装的包管理工具。JavaScript-NPM类似于java-Maven。

命令行输入`node -v` 检查配置好了，配置npm的镜像仓库地址，再执行：

```bash
node -v
npm config set registry [http://registry.npm.taobao.org/](http://registry.npm.taobao.org/)
```

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%20102.png)

### 配置node的path环境变量：（勿忘）

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%20103.png)

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%20104.png)

## 在VSCode中启动人人开源项目：renren-fast-vue

然后去VScode的项目终端中输入 `npm install`，是要去拉取依赖（package.json类似于pom.xml的dependency），但是会报错，然后进行如下操作：

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%20105.png)

如果python报错：pfython错误的可以·官网下载python2.7然后`npm config set python + python安装文件夹` 比如： `npm config set python D:\Program Files\Python27\` 

然后在执行上一步操作`npm install` 命令。然后启动项目使用命令：`npm run dev`

![Untitled](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%20106.png)

VSCode中：

![[http://localhost:8001/#/login](https://hediancha-1312143060.cos.ap-shanghai.myqcloud.com/Untitled%20107.png) 密码均为：admin](02_%E8%99%9A%E6%8B%9F%E6%9C%BAOracle%20VM%20VirtualBox%E4%B8%8B%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA_docker%20redis%20mysql%20bac7ad8c1e42458f8a041a1c7e89aa80/Untitled%20107.png)

[http://localhost:8001/#/login](http://localhost:8001/#/login) 密码均为：admin

[](https://www.python.org/ftp/python/2.7.18/python-2.7.18.msi)

python2.7下载地址

### python2.7安装教程：

[Windows10安装Python2.7_fendo-CSDN博客_python2.7](https://blog.csdn.net/u011781521/article/details/53909151)

### 谷粒商城各种报错问题汇总：

[【谷粒商城】npm install node-sass报错问题的解决_努力充实，远方可期-CSDN博客](https://blog.csdn.net/hancoder/article/details/113821646)