### Nginx概述

1. 介绍

Nginx是一款轻量级的Web服务器/反向代理服务器及电子邮件代理服务器。其特点是占有内存少，并发能力强。

[Nginx官网](www.nginx.org)

2. 下载和安装

CentOS系统：

- 安装依赖包：yum -y install gcc pcre-devel zlib-devel openssl openssl-devel
- wget [安装地址]
- 解压 tar -zxvf nginx-1.16.1.tar.gz
- cd nginx-1.16.1
- ./configure --prefix=/usr/local/nginx
- make && make install

### Nginx常用命令

1. 查看版本号：./nginx -v
2. 检查配置文件正确性：./nginx -t

3. 启动和停止

启动：./nginx   停止：./nginx -s stop   启动之后查看进程：ps -ef | grep nginx

4. 重新加载配置文件：./nginx -s reload

### Nginx配置文件结构

1. 全局块

运行相关的全局配置

2. events块

和网络连接有关的配置

3. http块

代理、缓存、日志记录、虚拟主机配置

+ http全局块
+ server块

​			server全局块

​			location块

### Nginx具体应用

1. 部署静态资源

相较于Tomcat，部署静态资源的能力更加高效，将静态资源部署到nginx非常简单，只要将文件复制到nginx安装目录下的html目录下即可。

2. 反向代理

反向代理服务器位于用户和目标服务器之间，用户访问反向代理服务器就可以获得目标服务器的资源，反向代理服务器负责将请求转发给目标服务器。

3. 负载均衡

早期的网站流量和业务功能较简单，使用单台服务器即可满足基本需求，但是随着业务流量越来越多，需要多台服务器组成应用集群，进行性能的水平扩展以及避免单点故障出现。

+ 应用集群：将同一应用部署到多台机器上，组成应用集群，接收负载均衡器分发的请求，进行业务处理并返回响应数据
+ 负载均衡器：将用户请求根据相应的负载均衡算法分发到应用集群中的一台服务器进行处理