# 附录：依赖软件安装参考步骤

**仅供参考，根据不同的实际环境和软件版本，安装步骤和细节会有所不同**

**提示：Truora-Service本身是一个典型的JAVA + Springboot项目，可跨平台运行。只要熟悉JAVA系列的开发，在不同的环境均可运行该项目**



<span id="install_docker" />

## 安装 Docker

### 环境要求

| 操作系统         |  版本最低要求     |  
| ------------- |:-------|
| CentOS/RHEL |7.3（kernel >= 3.10.0-514）|
|Debian|Stretch 9  |
|Ubuntu|Xenial 16.04 (LTS)|

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

