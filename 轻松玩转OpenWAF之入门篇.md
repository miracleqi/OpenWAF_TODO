名称
====

OpenWAF快速入门，即从安装到上线测试的一个快速体验流程，包括安装，发布应用，查看日志，上线测试

Table of Contents
=================

* [名称](#名称)
* [安装](#安装)
* [发布应用](#发布应用)
* [日志](#日志)
* [规则](#规则)

安装
====

* [源码安装](#源码安装)
    * [Debian&Ubuntu](#debianubuntu)
    * [Others](#others)
* [Docker容器](#docker容器)

源码安装
--------

### Debian&Ubuntu  

1. 安装 openresty  

   写在安装前的注意事项：  
   
```txt
   注意1：OpenResty 要求 OpenSSL 最低版本在 1.0.2e 以上，但 apt-get 安装 openssl 不满足此版本，因此提供解决方法如下：  
       方法1. apt-get 使用 backports 源安装 openssl，如jessie-backports  
           echo "deb http://mirrors.163.com/debian/ jessie-backports main" >> /etc/apt/sources.list  
           apt-get update  
           apt-get install -t jessie-backports openssl  
       方法2. 下载 openssl 源代码，如 1.0.2h 版本  
           wget -c http://www.openssl.org/source/openssl-1.0.2h.tar.gz  
           ./config  
           make && make install  
           
           若 openssl version 输出的版本依旧低于 1.0.2e 版本，  
           则下面第三步编译openresty时通过 --with-openssl=/path/to/openssl-xxx/ 指定 openssl 安装路径  
           
    注意2：OpenResty 依赖 PCRE ，但通过 apt-get 安装无法开启 pcre-jit，解决方法：  
        下载 pcre 源代码，如pcre-8.40版本  
            wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.40.tar.gz  
            tar -xvf pcre-8.40.tar.gz  
            cd pcre-8.40  
            ./configure --enable-jit  
            make && make install  
            
            到此 pcre 已支持 jit ，也可在第三步编译 openresty 时通过 --with-pcre=/path/to/pcre-xxx/ 指定 pcre 安装路径  
```

    安装openresty依赖  
        apt-get install libreadline-dev libncurses5-dev libpcre3-dev libssl-dev perl make build-essential

    下载openresty源码
    
```txt
   1.1 cd /opt  
   1.2 wget -c https://openresty.org/download/openresty-1.11.2.1.tar.gz  
   1.3 tar -xzvf openresty-1.11.2.1.tar.gz  
```

2. 安装 OpenWAF  

```txt
   2.1 安装 OpenWAF 依赖
       apt-get install libgeoip-dev swig  
   2.2 下载 OpenWAF 源码
       cd /opt  
       git clone https://github.com/titansec/OpenWAF.git  
   2.3 将 nginx 配置文件移至 /etc 目录下
       mv /opt/OpenWAF/lib/openresty/ngx_openwaf.conf /etc  
   2.4 覆盖 OpenResty 的 configure
       mv /opt/OpenWAF/lib/openresty/configure /opt/openresty-1.11.2.1  
   2.5 将 OpenResty 第三方模块移至 OpenResty 目录下
       mv /opt/OpenWAF/lib/openresty/* /opt/openresty-1.11.2.1/bundle/  
   2.6 删除空目录 OpenWAF/lib/openresty  
       rm -rf /opt/OpenWAF/lib/openresty  
```

3. 编译 openresty  

```txt
   3.1 cd /opt/openresty-1.11.2.1/  
   3.2 ./configure --with-pcre-jit --with-ipv6 \  
                   --with-http_stub_status_module \  
                   --with-http_ssl_module \  
                   --with-http_realip_module \  
                   --with-http_sub_module  \
                   --with-http_geoip_module
   3.3 make && make install 
```

Docker容器
----------
```txt
1. pull docker images from repository
   docker pull titansec/openwaf

2. start-up docker
   2.1 docker run, named openwaf
       docker run -d -p 22:22 -p 80:80 -p 443:443 --name openwaf titansec/openwaf
   2.2 enter openwaf
       docker exec -it openwaf /bin/bash
    
PS:
#add bridge address, e.g. 192.168.39.12
    pipework br0 ContainerName ip/gateway
    如：
    pipework br0 openwaf 192.168.39.12/24@192.168.39.253

Problems
1. pipework: command not found

   git clone https://github.com/jpetazzo/pipework.git
   cp pipework/pipework /usr/local/bin/
   
2. Warning: arping not found; interface may not be immediately reachable

   apt-get install arping
```






最大连接数 = worker_processes * worker_connections/4
比如，worker_processes（进程数）是两个，要达到10W并发量，那么worker_connections就要配20W


