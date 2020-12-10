# 源码安装

### 1. 依赖环境

| 环境  | 版本               |
| ----- | ------------------ |
| Nginx | Nginx1.6或以上版本 |

`Nginx`安装请参考[附录](../../TrustOracle-Service/appendix.html#install_nginx)

### 2. 拉取代码

代码可以放在/data下面
执行命令：

    git clone https://github.com/WeBankBlockchain/TrustOracle-Web.git

在代码库中docs文件下有`Nginx`配置文件，直接可以拿来替换安装的 `Nginx` 的配置文件`Nginx.conf`；
然后修改`Nginx.conf`；

进入`Nginx`配置文件（这里Nginx安装在/usr/local下面，如果这里没找到，可以到/etc下寻找,如有权限问题，请加上sudo）

```
    cd /usr/local/nginx/conf
```

1、 修改web服务端口（端口需要开通策略且不能被占用）

```
    sed -i "s/3002/${your_server_port}/g" nginx.conf
```

例如：

```
    sed -i "s/3002/8080/g" nginx.conf   你修改的服务端口是8080
```

2、 修改服务`ip`

```
    sed -i "s/10.0.0.1/${your_server_ip}/g" nginx.conf
```

例如： 

```
    sed -i "s/10.0.0.1/192.168.0.1/g" nginx.conf
```

你修改的服务`ip`是192.168.0.1,也可以修改成域名

3、 修改静态文件路径

```
    sed -i "s/\/data\/TrustOracle-Web\/dist/${your_file_route}/g" nginx.conf
```

4、 修改`TrustOracle`服务`ip`和`端口`

```
    sed -i "s/10.0.0.1:8083/${your_TrustOracleServer_ipPort}/g" nginx.conf
````

按照上面的步骤执行后，可以直接跳过这一步骤，直接启动`Nginx`。若服务器已有`Nginx`可按照以下修改，增加一条server

```Nginx
    upstream trust_oracle_server{
        server 10.0.0.1:8083; #步骤三 Oracle管理服务地址及端口
    }
    server {
        listen       3002 default_server;   #步骤一 前端端口（端口需要开通策略且不能被占用）
        server_name  10.0.0.1;         #步骤一 前端地址，可配置为域名
        location / {
                root    /data/TrustOracle-web/dist;   #步骤二 前端文件路径(文件需要有权限访问)
                index  index.html index.htm;
                try_files $uri $uri/ /index.html =404;
                }

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location /oracle {
                    proxy_pass    http://trust_oracle_server/;    		
                    proxy_set_header		Host				$host;
                        proxy_set_header		X-Real-IP			$remote_addr;
                        proxy_set_header		X-Forwarded-For		$proxy_add_x_forwarded_for;
                }
        }
```

### 3. 启动Nginx

(1)、启动Nginx。
启动命令：

	/usr/local/nginx/sbin/nginx    (Nginx下载在/usr/local目录下)

检查Nginx是否启动成功

```
    ps -ef | grep nginx
```

观察进程是否起来

启动报错重点排查：日志路径是否正确（error.log和access.log）,Nginx有没有添加用户权限。

(2)、打开页面，页面`url`是`Nginx`配置的前端`ip`和`端口`。
例如:上面配置文件的`url`为   http://10.0.0.1:3002