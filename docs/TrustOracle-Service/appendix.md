# 附录

<span id="install_docker" />

## 安装 Docker

### 环境要求

| 操作系统         |  版本最低要求     |  
| ------------- |:-------|
| CentOS/RHEL |7.3（kernel >= 3.10.0-514）|
|Debian|Stretch 9  |
|Ubuntu|Xenial 16.04 (LTS)|

### 安装 Docker
Docker 官方提供了一键安装工具，可以方便的在主机上安装 Docker 工具。

选择一个镜像云安装 Docker 服务：

```Bash
# （推荐）使用 阿里云镜像 安装 Docker
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun;

# 使用 微软云镜像 安装 Docker
curl -fsSL https://get.docker.com | bash -s docker --mirror AzureChinaCloud;
```

如果安装出现下面类似，提示 `containerd.io` 版本错误：

```Bash  
Last metadata expiration check: 0:13:10 ago on Sun 08 Mar 2020 04:23:54 AM UTC.
Error: 
 Problem: package docker-ce-3:19.03.7-3.el7.x86_64 requires containerd.io >= 1.2.2-3, but none of the providers can be installed
  - cannot install the best candidate for the job
  - package containerd.io-1.2.10-3.2.el7.x86_64 is excluded
  - package containerd.io-1.2.13-3.1.el7.x86_64 is excluded
  - package containerd.io-1.2.2-3.3.el7.x86_64 is excluded
  - package containerd.io-1.2.2-3.el7.x86_64 is excluded
  - package containerd.io-1.2.4-3.1.el7.x86_64 is excluded
  - package containerd.io-1.2.5-3.1.el7.x86_64 is excluded
  - package containerd.io-1.2.6-3.3.el7.x86_64 is excluded
(try to add '--skip-broken' to skip uninstallable packages or '--nobest' to use not only best candidate packages)
```

需要执行一下下面的命令，然后再重新执行安装命令：

```Bash
# 安装 containerd.io 文件
yum -y install "https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.3.9-3.1.el7.x86_64.rpm"

# 重新执行 Docker 安装命令
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun;
```

* 启动 Docker

```Bash
# 启动 Docker
systemctl start docker
```

* 检测 Docker 安装

```Bash
# 启动 Hello World 容器
$ docker run --rm hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

如果出现以上输出，包含 `Hello from Docker!`，表示 Docker 安装成功。

### 安装 Docker-Compose
Docker-Compose 是 Docker 官方提供的基于单机的容器编排工具，可以很方便的在单台主机中管理多个容器，包括按照依赖顺序启动，关闭，重启等。

* 选择一种方式安装 Docker Compose：

```Bash
#（推荐）从 daocloud 下载 docker-compose 
curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose;

# 从 官网下载 docker-compose 
curl -L "https://get.daocloud.io/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m`" -o /usr/local/bin/docker-compose;

```

* `docker-compose` 文件添加执行权限

```Bash
# docker-compose 文件添加执行权限
chmod +x /usr/local/bin/docker-compose;
```

* 检测 Docker Compose 安装

```Bash
# 输出 Docker Compose 版本号
$ docker-compose -v
docker-compose version 1.27.4, build 40524192
```

<span id="install_java" />

## 安装 Java

### Ubuntu（Debian） 安装 Java

```Bash
# 安装默认Java版本(Java 8或以上)
sudo apt install -y default-jdk
# 查询Java版本
java -version 
```

<span id="centos_install_java" />

### CentOS 安装 Java

```Bash
# 查询 CentOS 原有的 Java 版本
$ rpm -qa|grep java

# 删除查询到的Java版本
$ rpm -e --nodeps java-[VERSION]

# 查询 Java 版本，没有出现版本号则删除完毕
$ java -version

# 创建新的文件夹，安装Java 8或以上的版本，将下载的jdk放在software目录
# 从 openJDK官网 (https://jdk.java.net/java-se-ri/8) 
#   或
# Oracle官网(https://www.oracle.com/technetwork/java/javase/downloads/index.html)
# 选择Java 8或以上的版本下载
# 例如下载jdk-8u201-linux-x64.tar.gz
$ mkdir /software

# 解压jdk 
$ tar -zxvf jdk-8u201-linux-x64.tar.gz

# 配置Java环境，编辑/etc/profile文件 
$ vim /etc/profile 

# 打开以后将下面三句输入到文件里面并退出
export JAVA_HOME=/software/jdk-8u201-linux-x64.tar.gz
export PATH=$JAVA_HOME/bin:$PATH 
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

# 生效profile
$ source /etc/profile 
# 查询Java版本，出现的版本是自己下载的版本，则安装成功。
java -version 
```

<span id="install_nginx" />


## 安装 Nginx

安装 `Nginx` 时，推荐使用 `yum`/`apt-get` 安装。

* **CentOS 7.x / RHEL 7.x**

```Bash
# 安装 EPEL 仓库
sudo yum install epel-release

# 更新仓库
sudo yum update

# 安装 Nginx
sudo yum install -y nginx
```

* **Ubuntu 16.04 / Debian 9**

```Bash
# 更新仓库
sudo apt-get update

# 安装 Nginx
sudo apt-get -y install nginx
```

* 测试是否安装成功

查看 `Nginx` 版本：

```Bash
$ nginx -v

nginx version: nginx/1.16.1
```

查看 `Nginx` 配置文件路径：

```Bash
$ nginx -t

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

## 安装 MySQL

此处以 `CentOS` 安装 `MariaDB` 为例。`MariaDB` 数据库是 `MySQL` 的一个分支，主要由开源社区在维护，采用 GPL 授权许可。`MariaDB` 完全兼容 `MySQL`，包括 API 和命令行。其他安装方式请参考[MySQL官网](https://dev.mysql.com/downloads/mysql/)。

（1）安装 `MariaDB`

- 安装命令

```Bash
sudo yum install -y mariadb*
```

（2）启停

```Bash
启动：sudo systemctl start mariadb.service
停止：sudo systemctl stop  mariadb.service
```

（3）设置开机启动

```
sudo systemctl enable mariadb.service
```

（4）初始化root用户

```shell
# 执行以下命令：
sudo mysql_secure_installation

#以下根据提示输入：

Enter current password for root (enter for none):<–初次运行直接回车
Set root password? [Y/n] <– 是否设置root用户密码，输入y并回车或直接回车
New password: <– 设置root用户的密码
Re-enter new password: <– 再输入一次你设置的密码
Remove anonymous users? [Y/n] <– 是否删除匿名用户，回车
Disallow root login remotely? [Y/n] <–是否禁止root远程登录，回车
Remove test database and access to it? [Y/n] <– 是否删除test数据库，回车
Reload privilege tables now? [Y/n] <– 是否重新加载权限表，回车
```

- 使用root用户登录，密码为初始化设置的密码

```Bash
mysql -uroot -p -h localhost -P 3306
```

- 授权root用户远程访问

```eval_rst
   .. important::

	- 注意，以下语句仅适用于开发环境，不能直接在实际生产中使用！！！ 以下操作仅供参考，请勿直接拷贝，请自定义设置复杂密码。
```

   

```sql
mysql > GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
mysql > flush PRIVILEGES;
```

```eval_rst
.. admonition:: 提示

     - 例子中给出的数据库密码（`123456`）仅为样例，强烈建议设置成复杂密码
     - 例子中 `root` 用户的远程授权设置会使数据库在所有网络上都可以访问，请按具体的网络拓扑和权限控制情况，设置网络和权限帐号
```
  **安全温馨提示：**

（5）创建test用户并授权本地访问

```sql
mysql > GRANT ALL PRIVILEGES ON *.* TO 'test'@localhost IDENTIFIED BY '123456' WITH GRANT OPTION;
mysql > flush PRIVILEGES;
```

（6）测试是否成功

- 登录数据库

```shell
mysql -utest -p123456 -h localhost -P 3306
```

- 创建数据库

```sql
mysql > create database datastash;
mysql > use datastash;

```

  **以上语句仅适用于开发环境，不能直接在实际生产中使用！！！以上设置会使数据库在所有网络上都可以访问，请按具体的网络拓扑和权限控制情况，设置网络和权限帐号**

<span id="install_git" />

## Git安装

Git：用于拉取最新代码

**CentOS:**:
```Bash
sudo yum -y install git
```

**Ubuntu**:
```Bash
sudo apt install git
```

<span id="cdn_instruction" />

## CDN 说明
// TODO