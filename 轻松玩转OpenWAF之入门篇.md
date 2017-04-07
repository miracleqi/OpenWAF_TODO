Name
====

OpenWAF快速入门，即从安装到上线测试的一个快速体验流程，包括安装，发布应用，查看日志，上线测试

Table of Contents
=================

* [Name](#name)
* [Installation](#installation)
* [Access_rule](#access_rule)
* [log](#log)
* [rule](#rule)

Installation
============

* [Resource](#Resource)
    * [Debian](#debian)
    * [Others](#others)
* [Docker](#docker)

Resource
--------

### Debian  

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

   安装openresty依赖详见 https://openresty.org/en/installation.html  

```txt
   1.1 cd /opt  
   1.2 wget -c https://openresty.org/download/openresty-1.11.2.2.tar.gz  
   1.3 tar -xzvf openresty-1.11.2.2.tar.gz  
```

2. 安装 OpenWAF  

```txt
   2.1 cd /opt  
   2.2 get OpenWAF  
       git clone https://github.com/titansec/OpenWAF.git  
   2.3 move OpenWAF configure  
       mv /opt/OpenWAF/lib/openresty/ngx_openwaf.conf /etc  
   2.4 cover openresty configure  
       mv /opt/OpenWAF/lib/openresty/configure /opt/openresty-1.11.2.1  
   2.5 the third-party modules move into openresty  
       mv /opt/OpenWAF/lib/openresty/* /opt/openresty-1.11.2.1/bundle/  
   2.6 remove OpenWAF/lib/openresty  
       rm -rf /opt/OpenWAF/lib/openresty  
```
3. 编译 openresty  

```txt
   3.1 cd /opt/openresty-1.11.2.1/  
   3.2 ./configure --with-pcre-jit --with-ipv6 \  
                   --with-http_stub_status_module \  
                   --with-http_ssl_module \  
                   --with-http_realip_module \  
                   --with-http_sub_module  
   3.3 make && make install 
```

最大连接数 = worker_processes * worker_connections/4
比如，worker_processes（进程数）是两个，要达到10W并发量，那么worker_connections就要配20W
