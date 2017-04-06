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
     
   安装openresty依赖详见 https://openresty.org/en/installation.html  
```
   1.1 cd /opt  
   1.2 wget -c https://openresty.org/download/openresty-1.11.2.2.tar.gz  
   1.3 tar -xzvf openresty-1.11.2.2.tar.gz  
```
2. 安装 OpenWAF  
```
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
```
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
