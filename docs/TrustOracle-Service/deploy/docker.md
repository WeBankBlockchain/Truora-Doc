# Docker 部署

```eval_rst
.. important:: 

     - 使用 Docker 部署 TrustOracle-Service 服务时，**仅支持 Linux 操作系统！！**
```

## 安装介绍
Docker 部署是通过 Docker 运行一个 TrustOracle-Service 服务，不包含 TrustOracle-Web。

如果需要 **同时** 使用 Docker 部署 TrustOracle-Service 和 TrustOracle-Web 服务，请参考：[独立部署](../../TrustOracle-Install/docker-single.html)

## 前置依赖

在使用本组件前，请确认系统环境已安装相关依赖软件，清单如下：

| 依赖软件 | 说明 |
| --- | --- | 
| Docker | >= 18.06.0+ | 
| Docker-Compose | >= 1.24 | 

关于安装 Docker 和 Docker Compose 服务，请参考：[安装 Docker](../appendix.html#install_docker) 。

## 编写配置文件

